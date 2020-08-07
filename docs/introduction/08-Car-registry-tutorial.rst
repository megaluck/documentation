====================================
Car Registry Tutorial
====================================

Car Registry App high level overview
####################################

The Car Registry app is an example of a sidechain that implements specific custom data and logic. The purpose of the application is to manage a simplified service that keeps
records of existing cars and their owners. It’s simplified as sidechain users will be able to register cars by simply paying a transaction fee, while in a real world scenario, 
the ability to create a car will be bound by the presentation of a certificate signed by the Department of Motor Vehicles or analogous authority, or some other consensus 
mechanism that guarantees that the car exists in the real world and it’s owned by a user with a given public key.
Accepting that in our example cars will just show up in sidechain, we want to build an application that can store information that identifies a specific car, such as vehicle 
identification number, model, production year, color (etc)... 
A car owner should be able to prove their ownership of the cars without disclosing information about their identity. We also want users to sell and buy cars,
with ZEN coins. 

User stories:
#############

1
**Q: I want to add my car to a Car Registry Sidechain.**

*A:* Create a new Car Entry Box, that contains car identification information (Unique can identifier, VIN, manufactures, model, year, registration number), and certificate. Proposition in this box is my public key in this Sidechain. When I create box Sidechain should check car identification information and certificate to be unique in this Sidechain.

2
**Q: I want to create sell order to sell my car using Car Registry Sidechain.**

*A:* I create a new Car Sell Order Box, that contains the price in coins and information from the Car Entry Box. So cars can exist in the Sidechain as a Car Entry Box or as a Car Sell Order, but not both at the same time. Also, this box contains the buyer’s public key. When I create a sell order Sidechain should check if there is no other active sell order with this Car Entry Box. Current Sell Order consists of the same information that consists of the Car Entry Box plus description.

3
**Q: I want to see all available Sell orders in Sidechain**

*A:* Have additional storage, which is managed by ApplicationState and stores all Car Sell Orders. All these orders can be retrieved using the new HTTP API call. 


4
**Q: I want to accept a sell order and buy the car.**

*A:* By accepting sell order I create a new transaction in the Sidechain, which creates a new Car Entry Box with my public key as proposition and transfers coins amount from me to the previous car owner.

5
**Q: I want to cancel my Car Sell Order.**

*A:* I create a new transaction, that contains Car Sell Order as input and Car Entry Box with my public key as proposition as output.

6.
**Q: I want to see my car entry boxes and car sell orders related to me (both created by me and proposed to me).**

*A:* Implement new storage that will be managed by the application state to store this information. Implement a new HTTP API, that contains a new method to get this information.

So, the starting point of the development process is the data representation. A car is an example of a non-coin box because it represents some entity, but not money. Another example of a non-coin box is a car that is selling. We need another box for a selling car because a common car box doesn't have additional data like sale price, seller proposition address, etc. For the money representation standard Regular Box is used (Regular box is coin box), that box is provided by SDK. Besides new entities CarBox and CarSellOrder we also need to define a way for creating/destroying those new entities. For that purpose new transactions shall be defined: transaction for creating a new car, transaction which moves CarBox to CarSellOrder, transaction which declares car selling, i.e. moving CarSellOrder to the new CarBox. All created transactions are not put into the memory pool automatically, so a raw transaction in hex representation shall be put by /transaction/sendTransaction API request. In summary, we will add the next car boxes and transactions:

+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Entity name         | Entity description                                                                                                                                                                                                    | Entity fields                                                                                                                                                                                                       |
+=====================+=======================================================================================================================================================================================================================+=====================================================================================================================================================================================================================+
| CarBox              | Box which contains car box data, which could be stored and operated in Sidechain                                                                                                                                      | boxData -- contains  car box data                                                                                                                                                                                   |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CarBoxData          | Description of the car by using defined properties                                                                                                                                                                    | vin -- vehicle identification number which contains unique identification number of the car                                                                                                                         |
|                     |                                                                                                                                                                                                                       | year -- vehicle year production                                                                                                                                                                                     |
|                     |                                                                                                                                                                                                                       | model -- car model                                                                                                                                                                                                  |
|                     |                                                                                                                                                                                                                       | color -- car color                                                                                                                                                                                                  |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CarSellOrderBox     | Box which contains car sell order data, which could be stored and operated in Sidechain.                                                                                                                              | boxData -- contains  car sell order data                                                                                                                                                                            |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CarSellOrderBoxData | Description of the car which is in sell status. That box data contains a special type of proposition SellOrderProposition. That proposition allows us to spent the box in two different ways: by seller and by buyer  | vin -- vehicle identification number which contains unique identification number of the car                                                                                                                         |
|                     |                                                                                                                                                                                                                       | year -- vehicle year production                                                                                                                                                                                     |
|                     |                                                                                                                                                                                                                       | model -- car model                                                                                                                                                                                                  |
|                     |                                                                                                                                                                                                                       | color -- car color                                                                                                                                                                                                  |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CarSellOrderInfo    | Information about car’s selling as well as proof of a current car owner. Used in transaction processing.                                                                                                              | carBoxToOpen -- car box for start selling                                                                                                                                                                           |
|                     |                                                                                                                                                                                                                       | proof -- proof for open initial car box                                                                                                                                                                             |
|                     |                                                                                                                                                                                                                       | price -- selling price                                                                                                                                                                                              |
|                     |                                                                                                                                                                                                                       | buyerProposition -- current implementation expect to have the specific buyer which had been found off chain. Thus during creation of car sell order we already know buyer and shall put his future car proposition  |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CarBuyOrderInfo     | Data required for buying a car or recall a car sell order. Used in transaction processing.                                                                                                                            | carSellOrderBoxToOpen -- Car sell order box to be open                                                                                                                                                              |
|                     |                                                                                                                                                                                                                       | proof -- specific proof of type SellOrderSpendingProof                                                                                                                                                              |
|                     |                                                                                                                                                                                                                       | for confirming buying of the car or recall car sell order                                                                                                                                                           |
+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Special proposition and proof:
##############################

    a) **SellOrderProposition** Standard proposition only contains one public key, i.e. only one specific secret key could open that proposition. 
       However, for a sell order we need a way to open and spend the box in two different ways, so we need to specify a additional proposition/proof. 
       SellOrderProposition contains two public keys: ``ownerPublicKeyBytes`` and ``buyerPublicKeyBytes``. So the seller or buyer private keys could open that proposition.
    |
    b) **SellOrderSpendingProof** The proof that allows us to open and spend ``CarSellOrderBox`` in two different ways: opened by the buyer and thus buy the car or opened by the seller and thus recall car sell order. Such proof creation requires two different API calls but as a result, in both cases, we will have the same type of transaction with the same proof type. 


Transactions:
#############

AbstractRegularTransaction 
**************************

Base custom transaction, all other custom transactions extend this base transaction. 

        *Input parameters are:*
        
            ``inputRegularBoxIds`` - list of regular boxes for payments like fee and car buying
            ``inputRegularBoxProofs`` - appropriate list of proofs for box opening for each regular box in ``inputRegularBoxIds``
            ``outputRegularBoxesData`` - list of output regular boxes, used as the change from paying a fee, as well as a new regular box for payment for the car.
            ``fee`` - transaction fee
            ``timestamp`` - transaction timestamp

        *Output boxes:*
                
            Regular Boxes created by change or car payment 

CarDeclarationTransaction
*************************

Transaction for declaring a car in the Sidechain, this transaction extends ``AbstractRegularTransaction`` thus some base functionality already is implemented. 

        *Input parameters are:*
        
            ``inputRegularBoxIds`` -- list of regular boxes for payments like fee and car buying
            ``inputRegularBoxProofs`` -- appropriate list of proofs for box opening for each regular box in inputRegularBoxIds
            ``outputRegularBoxesData`` -- list of output regular boxes, used as change from paying a fee, as well as a new regular box for car payment.
            ``fee`` -- transaction fee
            ``timestamp`` -- transaction timestamp
            ``outputCarBoxData`` -- box data which contains information about a new car.

        *Output boxes:*
        
            New CarBox with new declared car

SellCarTransaction 
******************

Transaction for starting selling of the car.

         *Input parameters are:*
         
            ``inputRegularBoxIds`` - list of regular boxes for payments like fee and car buying
            ``inputRegularBoxProofs`` - appropriate list of proofs for box opening for each regular box in inputRegularBoxIds
            ``outputRegularBoxesData`` - list of output regular boxes, used as change from paying fee, as well as new regular box for payment for car.
            ``fee`` -- transaction fee
            ``timestamp`` - transaction timestamp
            ``carSellOrderInfo`` - information about car selling, including such information as car description and specific proposition ``SellOrderProposition``.

        *Output boxes:*
         
            CarSellOrderBox which represents the car to be sold, that box could be opened by the initial car owner or specified buyer in case if buyer buys that car.         

BuyCarTransaction 
*****************

This transaction allows us to buy a car or recall a car sell order. 

        *Input parameters are:*
        
            ``inputRegularBoxIds`` - list of regular boxes for payments like fee and purchasing the car 
            ``inputRegularBoxProofs`` - appropriate list of proofs for box opening for each regular box in inputRegularBoxIds
            ``outputRegularBoxesData`` - list of output regular boxes, used as change from paying fee, as well as a new regular box for payment for the car.
            ``fee`` - transaction fee
            ``timestamp`` - transaction timestamp
            ``carBuyOrderInfo`` - information for buy car or recall car sell order.      
            
        *Output boxes:*
        
            Two possible outputs are possible. In the case of buying car: new CarBox with new owner, new Regular box with a value declared in carBuyOrderInfo for former owner of the Car. 

Car registry implementation
###########################

First of all we need to define new boxes. 
As described before, a Car Box is a non-coin box. As defined before we need Car Box Data class as well for describing custom data. So we need to define CarBox and CarBoxData as separate classes for setting proper way to serialization/deserialization. 

Implementation of CarBoxData:
*****************************

CarBoxData is implemented according description from “Custom Box Data Creation” chapter as ``public class CarBoxData extends AbstractNoncedBoxData<PublicKey25519Proposition, CarBox, CarBoxData>`` with custom data as:
    private final BigInteger vin;
    private final int year;
    private final String model;
    private final String color;
