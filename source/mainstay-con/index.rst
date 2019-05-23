Mainstay Service Protocol
===========================

This document describes the overall design and principles of the Mainstay connector service protocol which is used to provide trustless immutability to third party systems as a service. This immutability is derived from the Bitcoin blockchain Proof-of-Work in an extensible, scalable and efficient way. 

Overview
--------

The primary purpose of the Mainstay scheme is to provide a cryptographic *Proof of Immutable State* (PoIS) for a succession of changing states of some arbitrary system or process - i.e. proof that the sequence of states has only a single, linked, verifiable history that cannot be altered (or double-spent). This PoIS is obtained via the trustless immutability inherent to the Bitcoin blockchain, where proof-of-work (via the *permissionless* mining of blocks to extend the chain) creates a practically irreversible and incorruptible ordering of transactions that does not rely on trust in any entity. The external system with a 'sequence of changing states' that can be proven as immutable via the Mainstay protocol may in some instances be a separate blockchain (i.e. a *sidechain*). However, there are many other systems and processes where a PoIS (which is a proof of a single verifiable history) is of substantial value, such as in document tracking processes, critical software development and organisational governance. 

The underlying mechanism of the mainstay protocol is a sequence of successive `commitments <https://en.wikipedia.org/wiki/Commitment_scheme>`_ to a *fan-in-only* sequence of linked `transactions <https://en.bitcoin.it/wiki/Transaction>`_ on the Bitcoin blockchain, where each transaction has only a single output - referred to as the *staychain*. By enforcing the rule that all the transactions in the staychain can only have a one output, the staychain can only have a single, non-branching history from the base of the chain to the tip. Following this rule, the staychain state is as immutable as the Bitcoin blockchain and is backed by its immense proof-of-work. Verifiable state commitments to the staychain are then also immutable, and the immutability of any sequence of committed states can be proven by verifying the validity of the staychain. 

State commitments are made to staychain transactions using the homomorphic *pay-to-contract* scheme, where the public key of the output is modified verifiably by the commitment value. This enables the commitments to be verified independently while the staychain remains indistinguishable from other standard bitcoin transactions. The commitment embedded in a particular staychain transaction output consists of a single 256 bit number, however this can in turn incorporate a number of separate commitments as a `\ *Merkel Tree* <https://en.wikipedia.org/wiki/Merkle_tree>`_ where the Merkle tree *root* is committed to the staychain and a *leaf* commitment inclusion can be verified via a Merkle path proof. 

In order to maintain the property of immutability for sequential commitments in sequential Merkle Trees anchored to the staychain, only one simple additional rule must be followed: each commitment from a particular seqeunce of states must always be be verifiably committed to the *same* position within the Merkle tree. If the commitment is always validated in the same position, then the sequence is as immutable (as in having only a single possible non-branching history) as the the root commitment into the staychain. 

.. image:: ms-cmr-blocks.png
    :width: 730px
    :alt: Connector
    :align: center

Schematic of the commitment of states from three slots to the Connector Merkle Root (CMR) which is then committed to the Bitcoin staychain, over three consecutive blocks. The sequence of commitments to a specified slot is as immutable as the the Bitcoin staychain. 


The Mainstay service protocol provides a mechanism for service users to access a specific position in the commitment Merkle tree (refered to as a *slot*\ ) which is then regularly committed to a unique Bitcoin staychain. This enables the provision of *Immutability as a Service* where a number of sidechain or other systems/processes can commit to and utilise a single Bitcoin staychain, at a substantially reduced cost (in terms of Bitcoin transaction fees) compared to operating a separate transaction staychain within Bitcoin for each individual application. The service provider operating the staychain, and the connection service, can agree service terms for each user and then assume responsibility for propagating the staychain and paying the Bitcoin fees. 

Commitment Merkle Tree
----------------------

A Merkle tree is a data structure that enables a list of cryptographic commitments to be compressed into a single Merkle root with efficient and secure verification. As a result of the binary tree structure, a cryptographic proof that a specified commitment is included in the derivation of a root can be verified with O(log n) complexity, and the proof requires only O(log n) storage. A Merkle tree is defined by hash function (i.e. SHA256) and an assignment function, which maps each node to the concatenation of the hashes of its child nodes. Each parent node `N` is then defined from the left (L) and right (R) child nodes as:

::

    N(Parent) = SHA256(N(L)||N(R))

Proof of the inclusion a commitment (as a leaf of the tree) is then generated from a traversal of the tree from the leaf through to the root, and is authenticated by verifying the path of concatenated hashes. However - for the connector protocol - the additional requirement in order to prove immutability across successive commitments is that a particular sequence of successive commitments from an external (client) process are included in the corresponding sequence of Commitment Merkle Trees (CMTs) in the **same** leaf position each time the root is committed to the Bitcoin staychain. This specific Merkle leaf position is referred to as a *slot* and is designated by an integer ``slotid``. 

The ``slotid`` is defined according to the binary *path* from the leaf through to the Merkle root, which consists of the sequence of ``L`` and ``R`` concatenations (see Fig. 2). The ``slotid`` defined in this way does not change as the tree is extended with more slots and the depth of the tree is increased (increasing the depth of the tree will simply increase the size of the proofs). 

.. image:: slot-proof.png
    :width: 700px
    :alt: Slot proof
    :align: center

Schematic of the structure of a CMT with 8 leaves, where the leaf position (slot) is determined by the path. The sequence of concatenated hashes from the leaf through to the root forms a slot-proof that a commitment was made is a specified position. 


Slot-proofs
^^^^^^^^^^^

The Mainstay service maintains a current version of the full tree as commitments are added from users via slots (see below). If a slot is not active (i.e. is not associated with a client or user) the corresponding leaf commitment is set to zero. Once the root of the current updated tree (CMR) is committed into a new staychain transaction, then *slot-proofs* are generated for each ``slotid`` with a submitted commitment. The slot-proof consists of the hash sequence and concatenation order for the specific Merkle path to the commitment Merkle Root (CMR). 

The slot-proof for a specific ``slotid`` provides cryptographic proof that a particular commtment ``Com`` was committed to a specified staychain (identified by the *base* transaction ID ``TxID[0]``) at a staychain height ``txheight`` and at that specific slot position. 

Example slot-proof:

.. code-block:: json

   {
       commitment: "1a39e34e881d9a1e6cdc3418b54aa57747106bc75e9e84426661f27f98ada3b7",
       ops: [
           {
               append: true,
               commitment: "3a39e34e881d9a1e6cdc3418b54aa57747106bc75e9e84426661f27f98ada3b7"
           },
           {
               append: false,
               commitment: "4a39e34e881d9a1e6cdc3418b54aa57747106bc75e9e84426661f27f98ada3b7"
           },
       ],
       merkle_root: "5a39e34e881d9a1e6cdc3418b54aa57747106bc75e9e84426661f27f98ada3b7"
   }

To obtain a Proof of Immutable State (PoIS) one or more slot-proofs on same staychain and with the same ``slotid`` are required as described below. 

Slot connection
---------------

Individual users (clients) of the connector service are granted exclusive permission to add a 32 byte commitment to a specific ``slotid`` for as long as a service agreement remains in force. Upon the commencement of a service agreement with a client, the client will be assigned a free ``slotid`` (the lowest number currently unused). The client will then provide a *validation script* ``PubKeyScript`` which contains the policy for authenticating a submitted commitment. The policy is determined by the client, and can be a single public key requiring a single commitment signature or an *m-of-n* multisignature script (or any other policy logic). In addition, the client will be provided with API access details and tokens. 

.. image:: slots-list.png
    :width: 700px
    :alt: Slot list
    :align: center

Schematic of a CMT with 8 slots. The mapping to the active slot list (ASL) is shown. 


On the initiation of a connection, the ``PubKeyScript`` is added to the *active slot list* (ASL) in the position corresponding to ``slotid``. The connector service API then recieves signed commitments (signed in accordance with the ``PubKeyScript`` policy) from the client and the signatures are verified using the ``PubKeyScript``. If the signatures are valid then the commitment is added to the CMT at the ``slotid`` position. The connector server updates the cached CMT root each time a new slot commitment is recieved and verified. New verified commitments arriving for a particular slot overwrite the pervious commitment. 

At intervals determined by the staychain attestation frequency, the commitment server performs commitments to the Bitcoin staychain following the BIP175 *pay-to-contract* protocol. 

.. image:: msc-flow.png
    :width: 440px
    :alt: Commitment flow
    :align: center

Protocol and message flow for a user interacting with the service via a single slot. 

Once the commitment transaction has been confirmed, the commitment server then generates the slot-proofs for each of the active slots. These slot-proofs are then available to retrieve by the clients via the connector service API. 

Proof of Immutable State
------------------------

Clients retrieve slot-proofs from the connector service API in order to confirm a PoIS using a client side confirmation tool that queries a Bitcoin blockchain node via the RPC interface. The confirmation tool can be configured for a particular staychain and slot, which is defined by a *start point* Bitcoin ``TxID[s]``, the staychain ``xpub[i]`` and the ``slotid``. The start point transaction ID can be any staychain transaction before the transaction ID of the first slot-proof (the confirmation tool takes the slot-proof ``TxID[j]`` and traverses backward along the staychain until the ``TxID[s]`` is found). 

Any slot-proof can then be passed to the confirmation tool, which will determine whether the slot-proof (and hence state commitment) is committed to the specified staychain at the specified slot position. This is proof that the state commitment is part of the sequence defined by the staychain and slot position (if intermediate states also form a hash-chain, then each of the intermediate states is also proven immutable). Alternatively, the confirmation tool can determine whether any two slot-proofs are on the *same* slot position and staychain (irrespective of the configuration) - this is proof that both of the slot-proof commitments are part of the same immutable sequence. 

.. image:: ms-verification.png
    :width: 280px
    :alt: Verification
    :align: center

Verification pathways for state verification. Top: Verification that a particular state is committed to a particular staychain and ``slotid``. Bottom: Verification that a sequence (two or more) of states are committed to the same staychain and ``slotid`` is sequential transactions. 

Commitment frequency and fee policy
-----------------------------------

The service agreement with individual slot clients will specify the target staychain transaction frequency and fee policy. Due to the inherent nature of proof-of-work, the block generation interval on the Bitcoin blockchain is highly variable, and there is no guarantee of transaction confirmation in any particular time period which is also subject to the level of network congestion. 

The staychain policy will specify a target transaction period ``ctarget`` (e.g. 1 hour) and the connector server will generate and broadcast a new staychain transaction containing the CMR every ``ctarget`` interval (irrespective of how many Bitcoin blocks have been genrated). The transaction fee will initially be set at the value estimated (via a third party fee estimation app) for confirmation within 3 blocks, up to a maximum of value of ``maxfee``. ``maxfee`` (in BTC) is the maximum fee the service will pay per hour. In the case a transaction is not confirmed within 1 hour (due to network congestion and ``maxfee`` being insufficient) then the staychain transaction (updated with the latest CMR) is re-broadcast with an additional ``maxfee`` for the next 1 hour period (i.e. the fee will now be 2x ``maxfee``\ ) using the replace-by-fee (RBF) . This will then be repeated each ``ctagrget`` until the transaction is confirmed. 

The value of ``maxfee`` may be increased and ``ctarget`` decreased as more clients join the service, increasing the reliability and regularity of proofs. 

Staychain multi-signature security
----------------------------------

A fundamental property of the Mainstay protocol is that users do not have to trust the connector service (or anyone else) to guarantee immutability - this is provided by the global proof-of-work securing the Bitcoin blockchain combined with slot-proofs. However, in order to provide a continuous and reliable service, the staychain of commitment transactions must remain in the control of the connector service. If the private keys controlling the staychain output (i.e. the base private keys) are lost or stolen, then the new state commitments cannot be immutably linked, and users would be forced to coordinate updates to a new staychain. To provide the required security and resiliency of the service the staychain is controlled by a multi-sig script (as described in the whitepaper). In addition, each base private key (``xpriv[i]``) of the staychain is generated and secured inside of a BIP32-compatible hardware security module (HSM). 
