Ocean configuration
===================

Ocean nodes are configured via ``oceand`` command line arguments or parameters specified in the ``ocean.conf`` configuration file located in ``-datadir``. Ocean inherits all of the configuration options from *Elements 0.14* (which in turn inherits all of the configuration options from *Bitcoin 0.13*). Ocean specific configuration options are described below, with examples and whether they form part of the genesis block and are therefore critical to the definition of the chain. 

Configuration
-------------

.. code-block::

   chain=gold_main

   rpcuser=user
   rpcpassword=pass
   rpcport=18886
   rpcallowip=10.0.3.0/24
   txindex=1
   listen=1
   connect=nodeX:7042
   pkhwhitelist=1
   freezelist=1
   burnlist=1
   reindex=1
   rescan=1
   freezelistcoinsdestination=76a9149d2eaa0bb68b5b9ba11250994fdfce78f41fdc0188ac
   burnlistcoinsdestination=76a91415de997afac9857dc97cdd43803cf1138f3aaef788ac
   whitelistcoinsdestination=76a9144ff9b5c6885f87fb5519cc45c1474f301a73224a88ac
   initialfreecoinscoinsdestination=76a9142967e87b159cb36b1dec8d95382b35aef01a8ad788ac


   initialfreecoins=2100000000000000
   signblockscript=532103041f9d9edc4e494b07eec7d3f36cedd4b2cfbb6fe038b6efaa5f56b9636abd7b21037c06b0c66c98468d64bb43aff91a65c0a576113d8d978c3af191e38845ae5dab21031bd16518d76451e7cf13f64087e4ae4816d08ae1d579fa6c172dcfe4476bd7da210226c839b56b99af781bbb4ce14365744253ae75ffe6f9182dd7b0df95c439537a21023cd2fc00c9cb185b4c0da16a45a1039e16709a61fb22340645790b7d1391b66055ae
   con_mandatorycoinbase=51210282e9e791e3ade9242eee103284315d61933afcb5ae3006bd61560a5819dc9cd451ae
   initialfreecoinsdestination=52210333f1635e1140dcf12dfc25ab2b1f993e7d9f9fd69481808af7d57b5892fe2a6e21028e029a8383c812f7a1c64a5daeee0ac1df79dd5a0915654733ad0d5c2a7cda642103856c9deac8e061e7cb086922c73104869708a8081dc38e1c4290d6d409476cc553ae
   issuecontrolscript=52210333f1635e1140dcf12dfc25ab2b1f993e7d9f9fd69481808af7d57b5892fe2a6e21028e029a8383c812f7a1c64a5daeee0ac1df79dd5a0915654733ad0d5c2a7cda642103856c9deac8e061e7cb086922c73104869708a8081dc38e1c4290d6d409476cc553ae

   attestationhash=18b4355a10177cd6d1e11985086aa252e0a64ae59d95dcba0d882cdd99fa3564

Details
-------

Information on all the config arguments used, their importance and how to derive them.

--chain
^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes (but ocean defaults to test params)


Specifies the name of the chain. Chain name is important as based on this the client decides which chain parameters to use. Chain parameters differentiate in address/key prefixes and are thus crucial in generating multisig scripts for other config arguments and also when importing a private keys to full node wallets.

Currently the following hardcoded names exist (from ``chainparamsbase.h``\ ):

.. code-block::

   #define CHAINPARAMS_OCEAN_MAIN "ocean_main"
   #define CHAINPARAMS_OCEAN_TEST "ocean_test"
   #define CHAINPARAMS_GOLD_MAIN "gold_main"

Unlesss the ``--chain`` parameter is specified then the default **ocean_test** name is chosen.

Based on this parameter the chain params are chosen as (from ``chainparams.cpp``\ ):

.. code-block::

   std::unique_ptr<CChainParams> CreateChainParams(const std::string& chain)
   {
       if (chain == CBaseChainParams::MAIN)
           return std::unique_ptr<CChainParams>(new CMainParams(chain));
       if (chain == CBaseChainParams::GOLD)
           return std::unique_ptr<CChainParams>(new CGoldParams(chain));
       return std::unique_ptr<CChainParams>(new CCustomParams(chain));
   }

**Example values:**


* for ocean main: ``chain=ocean_main``
* for gold mainnet: ``chain=gold_main``
* for gold test: ``chain=gold_test`` or any other value

**Note:**

Creating custom parameters requires corresponding changes to the electrum server. Configuration options for the electrum server live in ``cb-electrum-server/electrumx/lib/coins.py``. The best practice is to override the class Ocean or OceanTestnet, depending on whether it's a mainnet chain or a testnet chain, and override the address/key prefixes (mainnet only) **as well as** the ``GENESIS_HASH``.

**Code change PRs:**


* https://github.com/commerceblock/ocean/pull/73
* https://github.com/commerceblock/cb-electrum-server/pull/8
* https://github.com/commerceblock/cb-electrum-server/pull/9

terms and conditions
^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


The terms and conditions are required in the derivation of new ocean addresses, where the contract hash is used to tweak the corresponding priv/pub key (per BIP175). This hash is, for reference and validation, included in the genesis block of the chain, therefore any ocean node attempting to connect to a speficic chain that has this functionality enabled will need to have a copy of the terms and conditions in the datadir.

The terms and conditions are copied as part of building the Dockerfile but will need to be copied manually when running ocean independently. The latest contracts can be found in ``ocean/doc/$chain``. The ``chain`` name specified above is also used as the directory name under ``doc`` to specify where the contract for each chain is stored.

**Example:**

For ``chain=ocean_main`` contract ``doc/ocean_main/latest.txt`` is used and so on...

**Note:**

The same terms and conditions will need to be used by the electrum wallet client. A public copy will be provided.

**Code change PRs:**


* https://github.com/commerceblock/ocean/pull/74

scripts
^^^^^^^

In order to generate multisig scripts and corresponding private keys a simple python `script <https://github.com/commerceblock/federation/blob/master/scripts/generate_multisig.py>`_ can be used.

This requires specifying number of keys, number of signatures and WIF (wallet private key format) prefix. The WIF can be found in the chosen chain parameters (named SECRET_KEY).

**Current values:**


* Main Params (ocean main): 128
* Gold Params (gold main): 180
* Custom Params (any other chain / testnet): 239

--issuecontrolscript
^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


Script determining ownership of the issuance process.

.. code-block::

   $ ./scripts/generate_multisig.py 2 3 180
   keys
   ['TdqZMPkmMEsaLyDvfxSabkr2bJTtLgGrZMNm2W86HH4qBjkFCtGy', 'TeKonFZRpCkUVxNaT8b4M3Ap3VFKgRYQTt2TjYn6mgHZZC6FaPGA', 'TmQVnMhAMHb5gzbbsV7x9rUcjZnjPJEtm9iNusFMQFDMetwLNPxK']
   script
   52210333f1635e1140dcf12dfc25ab2b1f993e7d9f9fd69481808af7d57b5892fe2a6e21028e029a8383c812f7a1c64a5daeee0ac1df79dd5a0915654733ad0d5c2a7cda642103856c9deac8e061e7cb086922c73104869708a8081dc38e1c4290d6d409476cc553ae

--initialfreecoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - no


Script destination for free coins, required for issuance. Usually same target as ``issuecontrolscript``. The number of free coins is specified by ``initialfreecoins``.

-freezelistcoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


Script destination for freezelist coins, required if freezelist is enabled (option -freezelist=1). 

-burnlistcoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


Script destination for burnlist coins, required if burnlist is enabled (option -burnlist=1). 

-whitelistcoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


Script destination for public key hash whitlelist coins, required if whitelist is enabled (option -pkhwhitelist=1). 

--con_mandatorycoinbase
^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - no


Script destination for all sidechain fees. Coinbase transaction of each new block pays all fees to this script.

.. code-block::

   $ ./scripts/generate_multisig.py 1 1 180
   keys
   ['TddnTbqyTFVYUvcVqqfSFwhUJkgeWjsn6k2VV38QhRSZf2h6GzAf']
   script
   51210282e9e791e3ade9242eee103284315d61933afcb5ae3006bd61560a5819dc9cd451ae

--signblockscript
^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - no


The signblock script is responsible for block generation in the chain. On non-HSM chains a similar approach to the other scripts should be used.

For HSM chains the `Dockerfile <https://github.com/commerceblock/federation/blob/master/Dockerfile.hsm.init>`_ can be used to generate keys and provide the multisig script. This Dockerfile requires providing appropriate config/secrets for the HSM.

.. code-block::

   docker build --build-arg user_pin=$USER_PIN --build-arg key_label=$KEY_LABEL -f Dockerfile.hsm.init .
   .
   ..
   ...
   script: 532103041f9d9edc4e494b07eec7d3f36cedd4b2cfbb6fe038b6efaa5f56b9636abd7b21037c06b0c66c98468d64bb43aff91a65c0a576113d8d978c3af191e38845ae5dab21031bd16518d76451e7cf13f64087e4ae4816d08ae1d579fa6c172dcfe4476bd7da210226c839b56b99af781bbb4ce14365744253ae75ffe6f9182dd7b0df95c439537a21023cd2fc00c9cb185b4c0da16a45a1039e16709a61fb22340645790b7d1391b66055ae

--pkhwhitelist
^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - yes


Wether to enforce whitelisting rules at this node. Set to pkhwhiteliist=1 for signing nodes is whitelisting is to be used.

--pkhwhitelist-scan
^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - no
     - yes


Wether to scan the blockchain for whitelisted addresses and KYC public keys. pkhwhitelist-scan=1 is required for all client nodes for used to transact on the blockchain or whitelist new addresses if the signing nodes enforce whitelisting rules.

--rescan
^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - no
     - yes


Rescan the blockchain for wallet addresses when restarting nodes, or adding new private keys to the wallet. Set rescan=1 for all nodes in the network using either pkhwhitelist=1 or pkhwhitelist-scan=1.

--reindex
^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - no
     - yes


Rescan the UTXO set when restarting nodes. Set reindex=1 for all nodes in the network using either pkhwhitelist=1 or pkhwhitelist-scan=1.

--attestationhash
^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - genesis-hash critical
     - Optional
   * - yes
     - no (if using Mainstay)


Reference to a transaction id hash in the mainstay staychain. Either first/latest staychain hash or the hash at which our chain genesis was committed.