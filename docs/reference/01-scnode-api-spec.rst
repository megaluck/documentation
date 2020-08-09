Sidechain Node API spec
~~~~~~~~~~~~~~~~~~~~~~
=====
**Sidechain Block operations**
=====


.. http:post:: /block/findById

*Find Block by ID*

**Parameters**

+---------+--------+----------+------------------+
| Name    | Type   | Required | Description      |
+=========+========+==========+==================+
| blockId | String | yes      | Find block by ID |
+---------+--------+----------+------------------+

**Example request**:

.. tabs::

   .. tab:: Bash

      curl -X POST "http://127.0.0.1:9085/block/findById" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"blockId\":\"0...6\"}"

   |
   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Vary: Accept
      Content-Type: text/javascript

      {
         "result":{
            "blockHex":"string",
            "block":{
               "id":"string",
               "parentId":"string",
               "timestamp":0,
               "mainchainBlocks":[
                  {
                     "header":{
                        "mainchainHeaderBytes":"string",
                        "version":0,
                        "hashPrevBlock":"string",
                        "hashMerkleRoot":"string",
                        "hashReserved":"string",
                        "hashSCMerkleRootsMap":"string",
                        "time":0,
                        "bits":0,
                        "nonce":"string",
                        "solution":"string"
                     },
                     "sidechainRelatedAggregatedTransaction":{
                        "id":"string",
                        "fee":0,
                        "timestamp":0,
                        "mc2scTransactionsMerkleRootHash":"string",
                        "newBoxes":[
                           {
                              "id":"string",
                              "proposition":{
                                 "publicKey":"string"
                              },
                              "value":0,
                              "nonce":0,
                              "activeFromWithdrawalEpoch":0,
                              "typeId":0
                           }
                        ]
                     },
                     "merkleRoots":[
                        {
                           "key":"string",
                           "value":"string"
                        }
                     ]
                  }
               ],
               "sidechainTransactions":[
                  {

                  }
               ],
               "forgerPublicKey":{
                  "publicKey":"string"
               },
               "signature":{
                  "signature":"string"
               }
            }
         },
         "error":{
            "code":"string",
            "description":"string",
            "detail":"string"
         }
      }      

_____

   .. http:post:: /block/findLastIds
   
*Returns an array with the ids of the last x blocks*  
   
**Parameters**

+---------+--------+----------+----------------------------------------+
| Name    | Type   | Required |          Description                   |
+=========+========+==========+========================================+
|  number |  int   |   yes    | Retrieves the last x number of blocks  |
+---------+--------+----------+----------------------------------------+
   
**Example request**:

.. tabs::

   .. tab:: Bash

      curl -X POST "http://127.0.0.1:9085/block/findLastIds" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"number\":10}"
      
      
**Example response**:

   .. sourcecode:: http
   
      {
         "result":{
            "lastBlockIds":[
               "055c15d9a6c9ae299493d241705a2bcfdfbc72a19f04394a26aa53b39f6ee2a6",
               "ae6bcf104b7a7cccf83dfa23494760fb8d9a4d5cc3de82443de8b82bb86669d1",
               "9120b0f8518d1944d4b0e8fac8990acc7dcb792ea660414906a03f346407160c",
               "e5b0e97df9502c9510e4862041754b62931c9dc0a4fa873b3a0d75561dcbe712",
               "6a080e3ee665980bf647b450749b04177fe272537808bb4aec70417f9994bd04",
               "97d1956ecb1199fe03171b0923dff4031850e33db56dd1afc3b5384350315d80",
               "2c3a4a91989110218a827f8baefa3a8e5baf33e7e16d32b2bdace94553478dde",
               "cf82fba3e75ac89ca7e8d1c29458b2d5eff9d807407d3265c14251da2c70b3b1",
               "d61da61b2c877f717fa50563a42cbad4420486bfa3b1f05d888528d69d8258d8",
               "921f9406d8edd03d2f5b65aa6f89e452720c7ef07244ee06f3ad19d2c49e45d8"
            ]
         }
      }

_____

 .. http:post:: /block/findIdByHeight
   
*Return a sidechain block Id by its height in a blockchain*  
   
**Parameters**

+---------+--------+----------+----------------------------------------+
| Name    | Type   | Required |          Description                   |
+=========+========+==========+========================================+
|  height |  int   |   yes    | Retrieves block ID by it´s height      |
+---------+--------+----------+----------------------------------------+
   
**Example request**:

.. tabs::

   .. tab:: Bash

      curl -X POST "http://127.0.0.1:9086/block/findIdByHeight" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"height\":100}"
      
      
**Example response**:

   .. sourcecode:: http
   
{
   "result":{
      "blockId":"e8c92a6c217a7dced190b729a7815f0be6a011ea23a38e083e79298bb66620e7"
   }
}


