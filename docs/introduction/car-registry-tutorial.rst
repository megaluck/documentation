====================================
Car Registry Tutorial
====================================

Car Registry App high level overview
************************************

The Car Registry app is then an example of a sidechain that implements specific custom data and logic. The purpose of the application is to manage a simplified service that keeps
records of existing cars and their owners. It’s simplified as sidechain users will be able to register cars by simply paying a transaction fee, while in a real world scenario, 
the ability to create a car will be bound by the presentation of say a certificate signed by the Department of Motor Vehicles or analogous authority, or some other consensus 
mechanism that guarantees that the car really exists in the real world and it’s owned by a user with a given public key.
Accepting that in our example cars will just show up in sidechain, we want to build an application that can store information that identifies a specific car, such as vehicle 
identification number, model, production year, color (etc)... 
We’ll also want that cars’ owners could prove their ownership of the cars without disclosing information about their identity. We also want users to sell and buy cars,
against ZEN coins. 

So, the starting point of the development process is the data representation. A car is an example of a non-coin box because it represents some entity, but not money. 
Another example of a non-coin box is a car which is selling. We need another box for a selling car because a common car box doesn't have additional data like sale price, 
seller proposition address etc. For the money representation standard Regular Box is used (Regular box is coin box), that box is provided by SDK. Besides new entities CarBox
and CarSellOrder we also need to define a way for creating/destroying those new entities. For that purpose new transactions shall be defined: transaction for creating new car, 
transaction which move CarBox to CarSellOrder, transaction which declare car selling, i.e. moving CarSellOrder to the new CarBox. All created transactions are not put into the
memory pool automatically, so a raw transaction in hex representation shall be put by /transaction/sendTransaction API request. In summary we will add next car boxes and 
transactions:

Entities: 

+------------------+-----------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
| Entity name      | Entity description                                                                      | Entity fields                                                                               |
+==================+=========================================================================================+=============================================================================================+
| CarBox           | Box which contains car box data, which could be stored and operated in Sidechain        | boxData -- contains  car box data                                                           |
+------------------+-----------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
| CarBoxData       | Description of the car by using defined properties                                      | vin -- vehicle identification number which contains unique identification number of the car |
|                  |                                                                                         | year -- vehicle year production                                                             |
|                  |                                                                                         | model -- car model                                                                          |
|                  |                                                                                         | color -- car color                                                                          |
|                  |                                                                                         | description -- car description                                                              |
+------------------+-----------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
| CarSellOrder     | Box which contains car sell order data, which could be stored and operated in Sidechain | boxData -- contains  car sell order data                                                    |
+------------------+-----------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+
| CarSellOrderData | Description of the car which are in sell status                                         | sellerProposition --  seller proposition, i.e. receiver of money for sold car.              |
|                  |                                                                                         | vin -- selling car vin                                                                      |
+------------------+-----------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------+

Transactions which allow to perform next boxes transitions

+------------------------------+--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
| Transaction name             | Input parameters                                             | Input parameters purpose                                                                                                                  | Output boxes       | Output boxes purpose                                                                                                |
+==============================+==============================================================+===========================================================================================================================================+====================+=====================================================================================================================+
| Car creation transaction     | Regular Box                                                  | For paying fee                                                                                                                            | Car Box            | Wanted new Car Box                                                                                                  |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | Fee value                                                    | How much fee will be paid for the transaction                                                                                             | Regular Box        | Change for fee                                                                                                      |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | Car proposition                                              | Owner car proposition as PublicKey25519Proposition                                                                                        |                    |                                                                                                                     |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | Vehicle identification number and any other car related data | Identification of the new car                                                                                                             |                    |                                                                                                                     |
+------------------------------+--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
| Car sell Order transaction   | Car Box                                                      | Box which identify car for selling, initial car box will be opened and no longer is valid, thus in any case new Car Box shall be created  | Car sell order Box | Representation of car in sell state, also contains additional information like seller coin box proposition address  |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | Seller proposition for coin box                              | Where money will be sent to                                                                                                               |                    |                                                                                                                     |
+------------------------------+--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
| Car buying order transaction | Car sell order Box                                           | Identify car for selling, contains seller coin box proposition address                                                                    | Car box            | New owner car box, with buyer proposition address                                                                   |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | Payment regular box id                                       | Id of box with money                                                                                                                      | Regular Box        | New coin box which could be opened by seller which contains coins for selling car                                   |
|                              +--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
|                              | buyerProposition                                             | Buyer proposition where money shall be sent                                                                                               |                    |                                                                                                                     |
+------------------------------+--------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+

Car registry implementation
***************************

First of all we need to define new boxes. 
As described before, a Car Box is a non-coin box. As defined before we need Car Box Data class as well for describing custom data. So we need to define CarBox and CarBoxData as separate classes for setting proper way to serialization/deserialization. 

So overall next classes will be created:

  ::
    
    public class CarBox extends AbstractNoncedBox<PublicKey25519Proposition, CarBoxData, CarBox>
 
  ::
    
    public class CarBoxSerializer implements BoxSerializer<CarBox>

  ::
    
    public class CarBoxData extends AbstractNoncedBoxData<PublicKey25519Proposition, CarBox, CarBoxData>



