***************************************
Internal representation of a Blockchain
***************************************

Being a distributed architecture, the sidechain software is meant to be delivered as a software application that will be compiled/installed by potentially many different independent, connected computers. In blockchain jargon, these computers are called “Nodes,” and the term “node” is also generally used to name also the blockchain software itself, the one run by each of the computers that participate in the blockchain.
So, the output of the Sidechain SDK, when customised by a developer, is a “Node” that implements some core functionalities, and the added logic.

A Node consists of 4 main elements: “**History**,” “**State**,” “**Wallet**,” and “**Memory pool**.” Before we get to know these 4 elements we need to know what a “box” is.

Concept of a BOX
****************

A box is a cryptographic object that was closed with some secret key(s) and that can be opened (i.e. spent, it cannot be opened again) by the owner of those keys. A box generalizes the concept of Bitcoin’s UTXOs.

Node Main elements
******************

  * **History**
    * “History” is a blockchain ledger, that is typically a list of Sidechain blocks that were received by the Node, and that have been verified against Consensus rules, and accepted.
  * **State**
    * “State” is a snapshot of all boxes that haven’t been opened yet. It represents the state at the current chain tip.
  * **Wallet**
    * The “Wallet” has two main functionalities:
      1. It holds the Secret keys that belong to that specific Node.
      2. It keeps track of objects that are of interest to this specific node, e.g. received coins (output boxes whose secret keys are known by the node) and views of them (e.g. balances).
  * **Memory Pool**
    * The “Memory pool” is a list of transactions that are known to the node but have not made it to a Sidechain block yet.
    


   

