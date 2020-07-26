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

  * :java:Implement BytesSerializable interface for CustomData, i.e.  functions byte[] bytes() and Serializer serializer(), also implement public static CustomData parseBytes(byte[] bytes) function for parsing from bytes


