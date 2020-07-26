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
  
  * :kbd:`Map<Byte, BoxSerializer<Box<Proposition>>> customBoxSerializers = new HashMap<>()`; where key is data type id and value is CustomSerializer for those data type id.



