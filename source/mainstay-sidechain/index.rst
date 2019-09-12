Sidechain integration
======================

This document describes the protocol for linking a blockchain to the Mainstay connector service to obtain trustlessly immutable transaction confirmations. This linked blockchain can in principle be public or private, permissioned or permissionless, but a defined entity (or federation of entities) will be responsible for performing state commitments to the service. In the case of a permissioned federated blockchain, this role can be performed by the block-signing nodes as they are relied upon to continue propagating the chain and confirming transactions in new blocks. Once a blockchain is *connected* to the service, it becomes a *sidechain* i.e. a blockchain that is linked to and dependent upon data encoded on a separate blockchain: the *mainchain*. In the CommerceBlock Mainstay serivce, the mainchain is the public blockchain with the highest security and greatest global cumulative proof-of-work: Bitcoin.

Slot initialisation
-------------------

The connection to the Mainstay service requires the reservation of a *slot* position which is designated with a ``slotid``. The slot reservation is activated via a service agreement with CommerceBlock for a specified duration, and requires the sidechain federation (or controller) to provide a ``PubKeyScript`` against which signatures required to authenticate the state commitments from given sidechain are validated. The service provider (CommerceBlock) then guarantees the operation of the service for the specified period, and provides the API access credentials for both committing the sidechain state to the slot, and retrieving the corresponding slot-proofs required for confirmation.

Sidechain initialisation
------------------------

To initialise the Mainstay connector, the *base* of the transaction staychain (\ ``TxID0``\ ) *and* the provided ``slotid`` in the Bitcoin blockchain must be committed to the sidechain in a defined location in order to enable sidechain state commitments to be trustlessly immutable. ``TxID0`` and ``slotid`` are concatenated and committed to the sidechain (they can be retrieved from the Mainstay API). This can in principle be in any position at any block-height (for pre-existing blockchains that have already been running for some time).

If this is an Ocean client based sidechain being linked before launch, then the hex encoded ``txid0||slotid`` is embedded in the genesis block using the ``attestationhash`` configuration option in the Ocean client. For example, if the ``slotid`` is 14 (\ ``0x0e``\ ) and ``txid0`` is ``b64f5fb1c36fe10fb74cd4797cef912cc43bdd0c2b2225fdacbbbea20b3bd365``\ , then in the ``ocean.conf`` file the following line is added:

``attestationhash=b64f5fb1c36fe10fb74cd4797cef912cc43bdd0c2b2225fdacbbbea20b3bd365``

This means that the unique and single Bitcoin staychain and slot position is now uniquely *paired* with the sidechain.

Sidechain state commitment
--------------------------

After the sidechain is initialised and begins state transitions (i.e. block production) via the federated block-signing nodes, the block producers
