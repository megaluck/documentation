Projects
~~~~~~~~


.. tabs::

   .. tab:: Bash

      curl -X POST "http://127.0.0.1:9085/block/findById" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"blockId\":\"055..e2a6\"}"

   
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


   :query sort: one of ``hit``, ``created-at``
   :query offset: offset number. default is 0
   :query limit: limit number. default is 30
   :reqheader Accept: the response content type depends on
                      :mailheader:`Accept` header
   :reqheader Authorization: optional OAuth token to authenticate
   :resheader Content-Type: this depends on :mailheader:`Accept`
                            header of request
   :statuscode 200: no error
   :statuscode 404: there's no user


.. tabs::

   .. tab:: Apples

      Apples are green, or sometimes red.

   .. tab:: Pears

      Pears are green.

   .. tab:: Oranges

      Oranges are orange.
