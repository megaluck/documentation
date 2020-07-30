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
  "result": {
    "blockHex": "019120b..000000",
    "block": {
      "header": {
        "version": 1,
        "parentId": "9120b0f8518d1944d4b0e8fac8990acc7dcb792ea660414906a03f346407160c",
        "timestamp": 1595475610,
        "forgerBox": {
          "nonce": -8596034112114319000,
          "id": "f290e648415642b051cf6075b5fcaa7609eddd9a919d144cc2062db632918d9e",
          "blockSignProposition": {
            "publicKey": "153623a54522cc0336068a305ac13f530f4fdc95ee105a7ee85939326b9996fb"
          },
          "typeId": 3,
          "vrfPubKey": {
            "valid": true,
            "publicKey": "d984ea8..000000"
          },
          "value": 10000000000,
          "proposition": {
            "publicKey": "153623a54522cc0336068a305ac13f530f4fdc95ee105a7ee85939326b9996fb"
          }
        },
        "forgerBoxMerklePath": "00000000",
        "vrfProof": {
          "vrfProof": "4a50bb992e..0a17b60000"
        },
        "sidechainTransactionsMerkleRootHash": "0000000000000000000000000000000000000000000000000000000000000000",
        "mainchainMerkleRootHash": "0000000000000000000000000000000000000000000000000000000000000000",
        "ommersMerkleRootHash": "0000000000000000000000000000000000000000000000000000000000000000",
        "ommersCumulativeScore": 0,
        "signature": {
          "signature": "37e8d00f8711478..170602da0571237b36dea04",
          "typeId": 1
        },
        "id": "ae6bcf104b7a7cccf83dfa23494760fb8d9a4d5cc3de82443de8b82bb86669d1"
      },
      "sidechainTransactions": [],
      "mainchainBlockReferencesData": [],
      "mainchainHeaders": [],
      "ommers": [],
      "timestamp": 1595475610,
      "parentId": "9120b0f8518d1944d4b0e8fac8990acc7dcb792ea660414906a03f346407160c",
      "id": "ae6bcf104b7a7cccf83dfa23494760fb8d9a4d5cc3de82443de8b82bb86669d1"
    }
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
