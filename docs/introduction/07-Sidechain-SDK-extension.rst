=======================
Sidechain SDK extension
=======================

******************
Data serialization
******************

Any data like Box/BoxData/Secret/Proposition/Proof/Transaction shall provide a way to  serialize itself to bytes and provide a way to parse it from bytes.
As was described before serialization is performed via special Serializer class. Any custom data, beside defining own Serializer and definition of parsing/serializing,
shall declare those Serializers for SDK, thus SDK will be able to use proper serializer for custom data. So full actions for describe serialization/parsing for some
CustomData are next:

* :kbd:`Implement BytesSerializable interface` for :kbd:`CustomData`, i.e. :kbd:` functions byte[] bytes()` and :kbd:`Serializer serializer()`, also implement :kbd:`public static CustomData parseBytes(byte[] bytes)` function for parsing from bytes
  
* Create :kbd:`CustomDataSerializer` and implement :kbd:`ScorexSerializer interface`, i.e. functions  :kbd:`void serialize(CustomData customData, Writer writer)` and :kbd:`CustomData parse(Reader reader)`;
  
* Provide a unique id for that data type by implementing a special function. For example for box data type it is the function  :kbd:`public byte boxTypeId()`, for other data types the function name could be different and you will be obliged to implement it. 
  
* :kbd:`Map<Byte, BoxSerializer<Box<Proposition>>> customBoxSerializers = new HashMap<>();` where key is data type id and value is CustomSerializer for those data type id.
  
* Add your custom serializer into the map, for example it could be something  like :kbd:`customBoxSerializers.put((byte)MY_CYSTOM_BOX_ID, (BoxSerializer) CustomBoxSerializer.getSerializer());`
  
* Bind map with custom serializers to your application:
::
 
 TypeLiteral<HashMap<Byte, Common serializer type>() {})
       .annotatedWith(Names.named(Bound property name))
       .toInstance(Created map with custom serializers);
       
Where Common serializer type and Bound property name could have next values



+--------------------------------+----------------------------------------+
| Bound property name            | Common serializer type                 |
+================================+========================================+
| CustomBoxSerializers           | BoxSerializer<Box<Proposition>>>       |  
+--------------------------------+----------------------------------------+
| CustomBoxDataSerializers       | NoncedBoxDataSerializer<NoncedBoxData  |
|                                | <Proposition, NoncedBox<Proposition>>> |           
+--------------------------------+----------------------------------------+
| CustomSecretSerializers        | SecretSerializer<Secret>>              |           
+--------------------------------+----------------------------------------+
| CustomProofSerializers         | ProofSerializer<Proof<Proposition>>    |        
+--------------------------------+----------------------------------------+
| CustomTransactionSerializers   |  TransactionSerializer<BoxTransaction  |                                  
|                                |  <Proposition, Box<Proposition>>>      |
+--------------------------------+----------------------------------------+

For example: 

::

  bind(new TypeLiteral<HashMap<Byte, BoxSerializer<Box<Proposition>>>>() {})
       .annotatedWith(Names.named("CustomBoxSerializers"))
       .toInstance(customBoxSerializers);

where  :kbd:`BoxSerializer<Box<Proposition>>>`  -- common serializer type :kbd:`"CustomBoxSerializers"` - bound property name 
:kbd:`customBoxSerializers` - created map with all defined custom serializers. Overall we have the next expected type and property name.

Custom box creation
*******************

  a) SDK Box extension Overview

To build a real application, a developer will need more than just receive, transfer and send coins back. A distributed app, built on a sidechain, will typically have to define some custom data that the sidechain users will be able to exchange according to a defined logic. Creation of new Boxes requires definition of new four classes. We will use name Custom Box as a definition for some abstract custom Box:


Class type
Class description
Custom Box Data class 
-- Contains all custom data definitions plus proposition for Box
-- Provide required information for serialization of Box Data
-- Define the way for creation new Custom Box from current Custom Box Data
Custom Box Data Serializer Singleton 
-- Define the way how to parse bytes from Reader into Custom Box Data object
-- Define the way how to put boxData object into Writer
Parsing/Serialization itself could be defined in Custom Box Data class
Custom Box
Representation new entity in Sidechain, contains appropriate Custom Box Data class
Custom Box Serializer Singleton
-- Define the way how to parse bytes from Reader into Box Data object
-- Define the way how to put boxData object into Writer
Parsing/Serialization itself could be defined in Box Data class



