Policy lists and control
==================================================

We propose an improvement to the Ocean and Elements protocols to enable sidechain federations to manage whitelisting and blacklisting of addresses on a federated sidechain by performing special transactions on the blockchain.

Introduction
------------

The bitcoin blockchain is a distributed ledger which enables users to transfer value in an uncensored manner. This poses problems for institutions or individuals who, for regulatory compliance reasons, are required by law need to know that the individuals they are transacting with are not on money laundering watchlists or similar. A solution to this is to create a federated sidechain, such as the Ocean sidechain, in which a group of entities collaborate to create a federation of Signing Nodes. These signing nodes form a trusted network that perform the function of Miners in the Bitcoin network, and with Ocean Mainstay are attested to the Bitcoin blockchain, leveraging the Bitcoin network's proof of work to prove immutability of their federated sidechain. The federated sidechain are then able to manage customer onboarding in collaboration with an appropriate KYC vendor to check a customer's identity and presence on AML watchlists. This is done by whitelisting customer addresses, which is a process of adding a customer's public keys to a whitelist. Only addresses on this whitelist are able to send and receive transactions. Furthermore, federation members require the ability to 'blacklist' customers if the need should arise, either by removing their address from the aforementioned whitelist, or by some other mechanism. We propose a mechanism which uses the Ocean blockchain to manage whitelisting and blacklisting by using a special 'black token' asset.

Problem: the current working model for the whitelisting/KYC system
------------------------------------------------------------------

In our current working model, the whitelisting is done by creating a series of 'whitelisting' nodes connected to the signing nodes. These nodes act as a gateway, blocking any transactions from non-whitelisted addresses from entering the mempool. The whitelisting nodes hold the whitelisted addresses in RAM for quick lookup, and obtain the whitelisted adddress from a MongDB database server. This model poses potential concerns because the whitelisted addresses are held in memory at a single point of failure and are therefore susceptible to manipulation. The whitelist is enforced at the mempool level: any transactions reaching the mempool are considered valid and can be signed to the blockchain. 

To make every node in the network a whitelisting node and store a local copy of whitelisted addresses does not improve the situation very much because a hacker could still potentially connect directly to the whitelist node. Then they would only need to manipulate the whitelist in their local node and the whitelist node in order to manipulate the whitelist.

Furthermore, transaction to/from non-whitelisted addresses remain in the mempool of the network for a period of time. Someone could fill the mempool with a flood of blocked transactions, thereby enabling a possible denial of service attack.

Finally, a hacker could manipulate the MongoDB whitelist, as the whitelisting node reads new whitelisted addresses from this database.

Solution: record whitelisting/blacklisting data in the immutable blockchain
---------------------------------------------------------------------------

We propose using the immutable federated sidechain, backed up by the Bitcoin proof of work using mainstay, to record whitelisting data. We propose both whitelisting and blacklisting in a two-pronged approach. 

Whitelisting and Blacklisting
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In an Ocean sidechain, the federation has the power to issue assets to customers by holding a special asset issuance token. We propose a special "white" token asset type which can be issued to customer addresses in an unspendable transaction. The whitelist status of an address is to be controlled using the amount of the white token held as a counter. The consensus rule will therefore be as follows:

*Addresses posessing an odd number of white tokens are whitelisted and therefore valid for transaction input/output.*

This rule is enforcable at both the mempool level and the blockchain level: transaction involving unwhitelisted addresses are kept out of the mempool. Furthermmore, the block signing rules mean that they cannot be included in a valid block. The rules are enforced by referring to the blockchain itself, which is immutable and backed up by the Bitcoin proof of work if using Mainstay.

Blacklisting
^^^^^^^^^^^^

A previously whitelisted address may later be required to be blacklisted to freeze funds for compliance reasons. The mechanism is simple: the federation issues another white token to the address to be blacklisted. 

Linking addresses to customer id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Addresses need to be associated with a particular customer id in order to link to an external id check database. This could be encoded in the metadata of a transaction in a confidential manner similar to "Confidential Transactions" to preserve anonymity such that only the federation members would be able to decode the customer id using N of X multisig. Furthermore, the metadata would be unique for every address so that two addresses could not be publicly associated with one another.

A link bewteen two different addresses could potentially be inferred if, for example, addresses from the same customer were to be included in the same block. One solution would be to stagger across blocks.

Further advantages to the proposal
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This method unambiguously and immutably proves the whitelisting history. This is a huge advantage in terms of compliance and transparency; there is no ambiguity surrounding whitelisting status at the time any transaction was made. The federation are able to prove when a particular address was blacklisted (or whitelisted) if needed.

Anonymity preserving options
----------------------------

The above method does not preserve anonymity on because each address is associated with the same whitetoken on the public blockchain. Some modifications to the above process that preserve anonymity are outlined below.

**Anonymity preserving methods**

*Option 1*

Each user owns a certain number of white token assets (an odd number of white tokens if whitelisted). The consensus rules will state that transactions must include an additional challenge: users must provide proof of ownership of an odd amount of the whitetoken in order to complete a valid transaction. This will be done using a ring signature or some other method to preserve anonymity; the user proves ownership of a whitetoken without revealing its address. The transaction will be carried out in two stages: 


#. The sender satifies the conditions of spending by proving ownership of a whitetoken and the asset to be spent. They broadcast a transaction which includes an additional challenge: to prove ownership of the receive address and ownership of a whitetoken. This transaction is regarded by the signing nodes as valid but incomplete until it times out.
#. The receiver wallet retreives the transaction from the mempool and signs it automatically to complete the transaction. The signing nodes now regard the transaction as valid and it is included in the blockchain.

In addition to preserving anonymity, this reduces the volume of data that has to be stored because each user only requires one whitetoken.

This mechanism ensures and proves immutably that senders and receivers were whitelisted at the time of the transaction without revealing the identities of the owners of the addresses involved.

*Option 2*

The user supplies a master public key. The federation sends the user a randomly generated user name, which determines the branch number of a HD wallet. The hash of the user name is used as the branch number of the HD tree. Any addresses generated from this public key and branch will be whitelisted. 


We outlined a draft proposal to store whitelisting data on the Ocean blockchain. We believe this proposal has advantages over the current "whitelisting node" system in the following key areas:

-maintainability
-security
-transparency and immutability
-availability

.. role:: raw-html-m2r(raw)
   :format: html


User onboarding
================================

A protocol is initially proposed to streamline the user onboarding process, and user self-registering of validated Ocean addresses.

Introduction
------------

The bitcoin blockchain is a distributed ledger which enables users to transfer value in an uncensored manner. This poses problems for institutions or individuals who, for regulatory compliance reasons, are required by law need to know that the individuals they are transacting with are not on money laundering watchlists or similar. A solution to this is to create a federated sidechain, such as the Ocean sidechain, in which a group of entities collaborate to create a federation of Signing Nodes. These signing nodes form a trusted network that perform the function of Miners in the Bitcoin network, and with Ocean Mainstay are attested to the Bitcoin blockchain, leveraging the Bitcoin network's proof of work to prove immutability of their federated sidechain. The federated sidechain are then able to manage customer onboarding in collaboration with an appropriate KYC vendor to check a customer's identity and presence on AML watchlists. This is done by whitelisting customer addresses, which is a process of adding a customer's public keys to a whitelist. Only addresses on this whitelist are able to form transaction outputs. Furthermore, federation members require the ability to 'blacklist' customers if the need should arise, either by removing their address from the aforementioned whitelist, or by some other mechanism. We propose a mechanism which uses the Ocean blockchain to manage whitelisting.

Outline of current system
-------------------------

The current development system of KYC and onboarding is as follows:

1 - User generates a number contract tweaked public keys and send these together with the corresponding raw public keys to the KYC vendor together with their I.D. data. Onfido generates a unique ID string for each customer.\ :raw-html-m2r:`<br>`
2 - Onfido forward the result to the sidechain commissioners via a webhook.\ :raw-html-m2r:`<br>`
3 - If the user passed the KYC/AML checks the sidechain commissioners save the addresses and pubkeys together with the unique ID string to a mongodb database. This is done automatically in 
the case of a "pass" result, otherwise the data are added to a "consider" mongodb database list and manual intervention is required to finally move the data to either "whitelist" or "blacklist" collection (a script has been written to facilitate this).
4 - The signing node monitors the mongo database for changes and updates its local list of whitelisted addresses accordingly (stored in RAM).

Outline of proposed new system
------------------------------

Preliminaries
^^^^^^^^^^^^^

1 - A HSM belonging to the commisioner creates a deterministic "wallet" key pairs {pub_c, priv_c} and publishes the master public key to the genesis block.

Onboarding
^^^^^^^^^^

1 - The user randomly selects a pub_c using the master public key published in the genesis block (see preliminaries above). This will serve as their unique user id.\ :raw-html-m2r:`<br>`
2 - The user sends an encrypted (with pub_c) address addr_e to the KYC vendor together with their pub_c.\ :raw-html-m2r:`<br>`
3 - The KYC vendor forwards the result of the checks together with addr_e and pub_c to a webhook.\ :raw-html-m2r:`<br>`
4 - If the user passed the KYC/AML checks then pub_c is recorded in the blockchain together with addr_e in a OP_REGISTERID transaction. This 
operation should be restricted to authorised nodes; this could be managed using a permission token. 

The signing nodes will build a map in RAM of pub_c:addr for convenience and speed.

User address self-registration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Submission
~~~~~~~~~~

1 - The user submits a transaction that includes the following information:  


* addr_e, ecrypted with pub_c  
* The operation code (OP_REGISTERADDRESS)  

Processing
~~~~~~~~~~

1 - The signing node looks up the pub_c from the addr:pub_c map using the transactions input address (users will request new addresses using existing addresses).\ :raw-html-m2r:`<br>`
2 - If the pub_c is already whitelisted, the node decrypts addr_e, adds it to the whitelist and updates the pub_c:addr map.  

Node restart
^^^^^^^^^^^^

In case of node restart, the whitelist is rebuilt from the blockchain.

Privacy
^^^^^^^

Access to the HSM is required in order to link users to addresses. 

Auditing
^^^^^^^^

Each user has their own pub/priv pair, so one user's addresses can be revealed if required by revealing their priv_c, without revealing any other user's addresses.

Advantages of the proposed new system
-------------------------------------


* Users only register the additional addresses they need, and pay a small price for the privilege via a transaction fee. This avoids bloating the whitelist records unneccessarily, and enables user to register as many new addresses as they require.
* Whitelist address registration is recorded immutably on the blockchain. Blacklisting could be performed similarly. This would be useful for auditing/compliance purposes.
* Registering new addresses is done independently by the user (features should be added to the wallet GUI to assist with this process).
* Contact hash can be registered on a per-user basis if desired, offering greater flexibility.
* ID keys and links to their associated addresses are kept of the blockchain but can be audited if necessary.

Disadvantages
-------------


* Users can potentially register unlimited addresses. However, this is offset by transaction fee profits. Registered addresses would presumably have been used anyway, therefore the additional bloat to the blockchain is minimal.

Code changes required
---------------------

Ocean
^^^^^

User id public keys
~~~~~~~~~~~~~~~~~~~


* Add new pub_c (id public keys) to the blockchain  
  .. code-block::

     - This will need to be carried out by the signing nodes.  
     - This should be done automatically when the pool of available pub_c gets low. A list of available pub_c will be kept in memory. When this list gets short,
     new pub_c will be inserted into the block. 
     - This should be done via a permissioned transaction...
     - This will need to be included in the block validation...
     - Alternatively, include a master public key and derivation path in the genesis block. Then users will be able to compute their own ID public keys.

OP codes
~~~~~~~~


* OP_REGISTERADDRESS  
* OP_REGISTERID
* OP_REGISTERIDPUBKEY

Map
~~~


* addr:pub_c map to enable pub_c lookup from address for the OP_REGISTERADDRESS transaction.  
* the inverse map for auditing purposes and for checking registration status of pub_c.  

Transaction verification
~~~~~~~~~~~~~~~~~~~~~~~~


* Verification of OP_REGISTERID transactions. Permission token logic.  
* Verification of OP_REGISTERADDRESS transactions.  

Permission token
~~~~~~~~~~~~~~~~


* A permission token that enables submission of OP_REGISTERID.
* This will take the form of a reissuance token that can reissue the WHITETOKEN token type.
* The wallet posessing the whitetoken reissuance token can issue a whitetoken to one of the pub_c. This indicates that a particular pub_c (and, by extension, user) is 
  whitelisted.
* We need to issue the WHITETOKEN type in the genesis block and label it as such. 

Keys and signing
~~~~~~~~~~~~~~~~


* Generation and storage of p_c key pair on a HSM device  
* Address decryption on HSM  

Startup
~~~~~~~

Scan blockchain and build whitelist on startup.

1 - Find OP_REGISTERID transactions and decode.
2 - Find OP_REGISTERADDRESS transactions and decode.

Electrum/ocean wallets
^^^^^^^^^^^^^^^^^^^^^^

Find unused pub_c
~~~~~~~~~~~~~~~~~


* If we use the "publish pub_c to blockchain" method, scan the blockchain for available pub_c.  
* If we use the deterministic pub_c method, generate the pub_c deterministically and compare to existing pub_c on blockchain until a unique one is found.

Prototype
~~~~~~~~~


* We should first build a prototype using the electrum and ocean wallets instead of a HSM.    

Plan
^^^^

1 - Write encryption/decryption functions in Ocean and Electrum.\ :raw-html-m2r:`<br>`
2 - Design transaction protocols and define OP_ codes.\ :raw-html-m2r:`<br>`
3 - Ocean wallet transaction builders.\ :raw-html-m2r:`<br>`
4 - Ocean wallet transaction parsers.\ :raw-html-m2r:`<br>`
5 - Edit Onfido API calls.\ :raw-html-m2r:`<br>`
6 - Blockchain scanners: whitelisted IDs, whitelisted addresses.\ :raw-html-m2r:`<br>`
7 - Whitetoken issuance: done on signing nodes. Set up multisig. Define parser for Onfido webhook message and consensus rules. Manual intervention in cases where check status 
is 'consider'.\ :raw-html-m2r:`<br>`
8 - Ocean wallet address registration RPCs.\ :raw-html-m2r:`<br>`
9 - Do the above for electrum as well.  

Elliptic Curve Integrated Encryption Scheme: ECIES
--------------------------------------------------

ECIES provides encryption, digital signature and and key exchange capabilities. This method of encryption can be implemented using the same elliptic curve cryptography 
functions that bitcoin uses `1 <chrome-extension://oemmndcbldboiebfnladdacbdfmadadm/https://www.researchgate.net/profile/Carmen_Sanchez_Avila/publication/255970113_A_Survey_of_the_Elliptic_Curve_Integrated_Encryption_Scheme/links/02e7e5212654222f0a000000.pdf>`_. Hence, bitcoin key pairs can be used to encrpyt data on the bitcoin blockchain.

In `1 <chrome-extension://oemmndcbldboiebfnladdacbdfmadadm/https://www.researchgate.net/profile/Carmen_Sanchez_Avila/publication/255970113_A_Survey_of_the_Elliptic_Curve_Integrated_Encryption_Scheme/links/02e7e5212654222f0a000000.pdf>`_\ , the two following standards are recommended as starting points for an implementation:


#. ISO/IEC 18033-2
#. SECG SEC1

Both these standards allow use of SHA-2 as a hash function, which bitcoin uses in the form of SHA-256. These standards allow use of AES for ecryption, which bitcoin uses for 
its wallet encryption scheme. 

Implementation in bitcoin core
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There is an implementation of the above available in a fork of the Bitcoin Core wallet:
https://github.com/stonecoldpat/Authenticated-Key-Exchange-Over-Bitcoin

We could use this as a starting point for our own implementation.

Implementation in Electrum wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Functions are available:

Encrypt:

.. code-block::

   cat | electrum encrypt $pk -


Decrypt:

.. code-block::

   electrum decrypt $pk ?


Note: this command will prompt for the encrypted message, then for the wallet password

The proposed system would build on the existing whitelist/KYC system to offer improved efficiency and flexibility, and would create an immutable record of address registration or user blacklisting on the blockchain.
