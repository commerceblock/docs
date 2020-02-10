Sidechain guide
======================

This guide describes how to link a *sidechain* to the Bitcoin blockchain via the *mainstay.xyz* connector service, in order to obtain a trustless proof of a unique single history of transactions for the sidechain. Here, the *sidechain* is any linked blockchain and can in principle be public or private, permissioned or permissionless, but a defined entity will be responsible for performing state commitments to the *mainstay.xyz* API. Once a blockchain is *connected* to the service, it becomes a Bitcoin *sidechain* i.e. a blockchain that is linked to and dependent upon data encoded on a separate blockchain: the *mainchain*. 

This guide assumes that the sidechain node/client instance has a Bitcoin-like API (i.e. with ``getbestblockhash`` RPC call) and that a 32+4 byte commitment can be made to the genesis block of the sidechain in a specified location. If the sidechain is based on the Ocean platform client, these commitments can be specified in the ``ocean.conf`` that defines the chain. 

The guide is split into three distinct sections for setting up the linking, performing state attestations by the permissioned entity and performing public verification. 

Initial set-up
^^^^^^^^^^^^^^^

The entity responsible for attesting the sidechain state to Bitcoin is called the *administrator*. The administrator is responsible for the agreement with the *mainstay.xyz* service for exclusive use of a connector service slot. This is obtained by signing up on the *mainstay.xyz* website, where the administrator will be given a slot position ``slot_id`` and an API access token to perform attestations ``token`` via email. For additional security, the administrator can optionally require that all sidechain attestations committed to the slot are signed by an administrator private key (the corresponding public key being provided on sign-up at *mainstay.xyz*). 

To generate this key, the administrator can use the Mainstay client. For example (where entroy is any string to add additional entropy to the keygen),

.. code-block:: bash

  msc keygen -g entropy

  > Private key: c66cb6eb7cd585788b294be28c8dcd6be4e37a0a6d238236b11c0beb25833bb9

This generates a hex encoded 32-byte private key (``privkey``). The corresponding public key can be generated as follows:

.. code-block:: bash

  msc keygen -g c66cb6eb7cd585788b294be28c8dcd6be4e37a0a6d238236b11c0beb25833bb9

  > Public key: 03fe0c17d00e5d5fc879dd1c2d4fbe3d61eb6d851ce3cd31173219aaf72e13fcd9

The generated public key is then supplied at sign-up. The private key is stored in the ``msc`` config file. 

Once the *mainstay.xyz* slot is active, the **base** staychain Bitcoin transaction ID can be retrived as follows (for ``slot_id = 3``):

.. code-block:: bash

  msc info -s 3

If the slot has been activated (usually within an hour of sign-up completion), the base ID will be returned. E.g. 

.. code-block:: bash

  > Base ID: 420d083de8ab078dbba5ea37f877cb35dd621e34f231cce997a16cd241449d51:3

If not active, the the following message will be returned:

.. code-block:: bash

  > Slot 7 not active.

Sidechain configuration
------------------------

The base ID (which is the staychain base TxID and slot ID) must then be committed to the sidechain genesis block to uniquely link the sidechain to a single slot-proof sequence. In the case of an Ocean platform sidechain, this means setting the following parameters in the ``ocean.conf`` chain definition file:

.. code-block:: bash

  attestationhash=420d083de8ab078dbba5ea37f877cb35dd621e34f231cce997a16cd241449d51
  mappinghash=00000000000000000000000000000000000000000000000000000000000000000003

The sidechain can then be launched and new blocks created at regular intervals (see the Ocean platform and block signing guide for more details). 

Attestation
^^^^^^^^^^^^

The adimistrator has access to a connected sidechain node. At a frequency equal to the block creation frequency of the sidechain, the administartor commits the hash of the sidechain tip to the mainstay.xyz slot:

.. code-block:: bash

  BLOCKHASH = `ocean-cli getbestblockhash`
  msc attest -c $BLOCKHASH -s 3 -t token

This would typically be performed automatically as a cron job. Alternatively, ``attestation-tool`` in the main ``mainstay`` application can be used. 

Verification
^^^^^^^^^^^^^

The previous steps are performed solely by the administrator. Verification can be performed by anyone who is verifying the sidechain (i.e. running a full sidechain node). In addition to having RPC access to the sidechain client (``oceand``), a verifier also requires the Mainstay client installed (``msc``) and RPC access to a full ``bitcoind`` node (alternatively a trusted block-explorer API can be used). 

To verify the full proof sequence for the sidechain and determine the latest sidechain attested block, the ``sync`` command of the Mainstay client can be used. This requires the sidechain ``oceand`` and ``bitcoind`` RPC credentials and URLs are provided (as ``-n`` and ``-b`` respectivly). To sync, simply run:

.. code-block:: bash

  msc sync -s 3 -n username2:password2@localhost:8336 -b username1:password1@localhost:8332 

If the verification is successful, the client will return the latest sidechain verified block. For example:

.. code-block:: bash

  Verified sidechain attestation sequence
  Latest attestated sidechain block: 47e3d796f0ae87f2261e620018ffb1e0458175e17faf2762f209a17c727a8690 height 163188

The ``msc`` client retrieves and verifies the full sequence of staychain commitments back to the base ID transaction. This may take some time if there is substantial history. The full slot proof sequence is written to the msc data directory (the location of which can be found by running ``msc config``). After the inital sync, further ``sync`` commands append to the saved proof sequence, taking much less time. 