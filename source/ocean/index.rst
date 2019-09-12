Introduction
============

Ocean is an open source federated blockchain and sidechain platform, which is developed and maintained by CommerceBlock. It incorporates  functionality for token issuance, on-chain transaction policy rules including address whitelisting and for linking to Bitcoin via the Mainstay protocol.

Ocean protocol based blockchains employ a *federated* consensus mechanism to extend the chain and produce new blocks. Blocks are validated when they acquire a threshold of signatures from pre-defined *signing nodes* - the public keys of these signing nodes define the blockchain and the permissions to create blocks. Federated blockchains provide Byzantine fault tolerance as well as enabling fast, regular block times (< 1 minute) and a high degree of scalability. In addition, the fixed validator set of a federated blockchain enables transaction and address policies to be enforced - this means that user addresses can be whitelisted, blacklisted and frozen by the federation controllers facilitating the KYC and user ID verification requirements of tokenised asset and securities issuers. A unique feature of the Ocean platform is that transaction control whitelists and freezelists (*policy lists*) can be administered entirely on-chain without any requirement for separate systems and databases.

The Ocean client (incorporating the full node and wallet) is derived from the open-source Elements project, which in turn was built from the main Bitcoin Core codebase. The core routines, data structures and cryptographic algorithms are the same as those used in the Bitcoin protocol, which is the most secure and battle-tested blockchain platform ever created.

.. note::
	Ocean is released under the terms of the MIT license.

.. hint::
	For a more extensive set of documentation for the Elements platform, including easy to understand descriptions of the underlying technologies and detailed tutorials and examples, visit `elementsproject.org <http://elementsproject.org>`_.

Contents
--------

.. toctree::
   :maxdepth: 2

   ../policy/index
   ../ocean-config/index
   ../running-node/index
   ../federation/index
   ../ocean-api/index
   ../ocean-wallet/index
