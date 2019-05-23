Application
=======================

The mainstay repository is an application that implements the Mainstay protocol - available at `github.com/commerceblock/mainstay <https://github.com/commerceblock/mainstay>`_. It consists of a Go daemon that performs attestations of the `Ocean <https://github.com/commerceblock/ocean>`_ network along with client commitments to Bitcoin in the form of a commitment merkle tree.

Mainstay is accompanied by a Confirmation tool that can be run in parallel with a Bitcoin network node to confirm attestations and prove the commitment inclusion in Mainstay attestations.

Prerequisites
-------------

* Go (https://github.com/golang)
* Bitcoin (https://github.com/bitcoin/bitcoin)
* Zmq (https://github.com/zeromq/libzmq)

Instructions
------------

Attestation Service
^^^^^^^^^^^^^^^^^^^


* 
  Install Go and the attestation service by following ``scripts/build.sh``

* 
  Setup up database collections and roles using ``scripts/db-init.js``

* 
  Setup ``conf.json`` file under ``/config`` by following `config guidelines </config/README.md>`_

* 
  Run service


  * Regtest mode

    * Run service: ``mainstay -regtest``
    * Run signer: ``go run $GOPATH/src/mainstay/cmd/txsigningtool/txsigningtool.go -regtest``
    * Insert commitments to "ClientCommitment" database collection in order to generate new attestations

  * 
    Testnet/Mainnet mode


    * 
      Download and run a full Bitcoin Node on testnet mode, fully indexed and in blocksonly mode.

    * 
      Fund this wallet node, send all the funds to a single (\ ``m of n`` sig) P2SH address and store the ``TX_HASH``\ , ``PRIVKEY_x`` and ``REDEEM_SCRIPT`` of this transaction, where ``x in [0, n-1]``.

        (In the case of an Ocean-type network the ``TX_HASH`` should be included in the genesis block using the config option ``attestationhash``\ )

    * 
      Follow the same procedure to generate a single (\ ``m of n`` sig) P2SH address used to topup the service and store the ``TOPUP_ADDRESS``\ , ``TOPUP_PRIVKEY_x`` and ``TOPUP_SCRIPT``.

    * 
      Run the mainstay attestation service by:

        ``mainstay``

        Command line parameters should be set in ``.conf`` file

    * 
      Run transaction signers of the m-of-n multisig P2SH addresses for ``x in [0, n-1]`` by:

        ``go run $GOPATH/src/mainstay/cmd/txsigningtool/txsigningtool.go -pk PRIVKEY_x -pkTopup TOPUP_PRIVKEY_x -host SIGNER_HOST``

        Command line parameters should be set in the corresponding signer ``.conf`` file


* Unit Testing

  * ``/$GOPATH/src/mainstay/run-tests.sh``

Mainstay configuration
----------------------

Sample Config
^^^^^^^^^^^^^

.. code-block:: json

   {
       "staychain": {
           "initTx": "87e56bda501ba6a022f12e178e9f1ac03fb2c07f04e1dfa62ac9e1d83cd840e1",
           "initScript": "51210381324c14a482646e9ad7cf82372021e5ecb9a7e1b67ee168dddf1e97dafe40af210376c091faaeb6bb3b74e0568db5dd499746d99437758a5cb1e60ab38f02e279c352ae",
           "initChaincodes": "0a090f710e47968aee906804f211cf10cde9a11e14908ca0f78cc55dd190ceaa,0a090f710e47968aee906804f211cf10cde9a11e14908ca0f78cc55dd190ceaa",
           "topupAddress": "2MxBi6eodnuoVCw8McGrf1nuoVhastqoBXB",
           "topupScript": "51210381324c14a482646e9ad7cf92372021e5ecb9a7e1b67ee168dddf1e97dafe40af210376c091faaeb6bb3b74e0568db5dd499746d99437758a5cb1e60ab38f02e279c352ae",
           "regtest": "1"
       },
       "main": {
           "rpcurl": "127.0.0.1:18000",
           "rpcuser": "USERNAME",
           "rpcpass": "PASSWORD",
           "chain": "regtest"
       },
       "clientchain": {
           "rpcurl": "127.0.0.1:19000",
           "rpcuser": "USERNAME",
           "rpcpass": "PASSWORD",
           "chain": "main"
       },
       "signer": {
           "publisher": "*:5000",
           "signers": "node0:1000,node1:1001"
       },
       "db": {
           "user":"user",
           "password":"pssword",
           "host":"localhost",
           "port":"27017",
           "name":"mainstay"
       },
       "fees": {
           "minFee": "5",
           "maxFee": "50",
           "feeIncrement": "2"
       },
       "timing": {
           "newAttestationMinutes": "60",
           "handleUnconfirmedMinutes": "60"
       }
   }

Config Parameters
^^^^^^^^^^^^^^^^^

Compulsory
**********

Currently ``main`` config category is compulsory. This should be made optional in the future as tools that do not require ``main`` rpc connectivity options use this.


* ``main`` : configuration options for connection to bitcoin node

  * ``rpcurl`` : address for rpc connectivity
  * ``rpcuser`` : user name for rpc connectivity
  * ``rpcpass`` : password for rpc connectivity
  * ``chain``\ : chain name for inner config, i.e. testnet/regtest/mainnet

The ``staychain`` category is compulsory and can be set from either .conf file or command line arguments. The configuration below is optional as preferred entry is via command line - `options <#command-line-options>`_.


* ``staychain`` : configuration options for staychain parameters

  * ``initTx`` : initial transaction sets the state for the staychain
  * ``initScript`` : initial script used to derive subsequent staychain addresses
  * ``initChaincodes``\ : chaincodes of init script pubkeys used to derive subsequent staychain addresses
  * ``topupAddress`` : address to topup the mainstay service
  * ``topupScript`` : script that requires signing for the topup

Several other subcategories become compulsory only if the base category exists in the ``.conf`` file.

For the base categories ``db`` and ``signer`` the following parameters are compulsory:


* 
  ``db`` : configuration options for database


  * ``user`` : db user name
  * ``password`` : db user password
  * ``host`` : db host address
  * ``port`` : db host port
  * ``name`` : db name

* 
  ``signer`` : zmq signer connectivity options


  * ``signers`` : list of comma separated addresses (host:port) for connectivity to signers

Optional
********

All the remaining conf options are optional. These are explained below:


* ``signer``

  * ``publisher`` : optionally provide host address for main service zmq publisher

Default values are set in ``attestation/attestsigner_zmq.go``.


* ``fees`` : fee configuration parameters for attestation service

  * ``minFee`` : minimum fee for attestation transactions
  * ``maxFee`` : maximum fee for attestation transactions
  * ``feeIncrement`` : fee increment value used when bumping fees

Default values are set in ``attestation/attestfees.go``


* ``timing`` : various timing configuration parameters used by attestation service

  * ``newAttestationMinutes`` : option in minutes to set frequency of new attestations
  * ``handleUnconfirmedMinutes`` : option in minutes to set duration of waiting for an unconfirmed transaction before bumping fees

Default values are set in ``attestation/attestservice.go``

Command Line Options
********************

Currently only parameters in the ``staychain`` category can be parsed through command line arguments.

These command line arguments are:


* ``tx`` : argument for initTx as above
* ``script`` : argument for initScript as above
* ``chaincodes``\ : argument for initChaincodes as above
* ``addrTopup`` : argument for topupAddress as above
* ``scriptTopup`` : argument for topupScript as above

Env Variables
*************

All config parameters can be replaced with env variables. An example of this is ``config/conf.json``.

The Config struct works by first looking for an env variable with the name set and if an env variable is not found then the config parameter is set to the actual value provided.

If the config argument is not to be used, **no value** should be set in the conf file. Warnings for invalid argument values are provided in runtime.

Client Chain Parameters
***********************

Parameters used for client chain confirmation tools and are not part of Config struct used by service.


* ``clientchain`` : configuration options for connectivity to client rpc node

Same configuration options as ``main``. The ``clientchain`` name can be replaced with any name to match the sidechain. See ``cmd/confirmationtool/conf.json``. This is not used by Config struct, only by ``config::NewClientFromConfig()``.


Tools
-----

Along with the Mainstay daemon there is various tools offered serving utilities for both Mainstay operators and clients of Mainstay. These tools and their functionality are briefly summarized below:

Transaction Signing Tool
^^^^^^^^^^^^^^^^^^^^^^^^

The transaction signing tool can be used by each signer of the mainstay multisig to sign transactions.

``go run $GOPATH/src/mainstay/cmd/txsigningtool/txsigningtool.go -pk PRIVKEY -pkTopup TOPUP_PRIVKEY -host SIGNER_HOST``

where:


* ``PRIVKEY``\ : private key of address initial funds were paid to
* ``TOPUP_PRIVKEY``\ : private key of the topup address
* ``SIGNER_HOST``\ : host address that the signer is publishing at and for the mainstay service to subscribe to

The tool subscribes to the mainstay service in order to receive confirmed attestation hashes and new bitcoin attestation transaction pre-images. These transactions are signed and broadcast back to the mainstay service.

To do the signing ECDSA libraries are used and and no Bitcoin node connection is required.

The live release of Mainstay will be instead using an HSM interface. Thus this tool is for testing purposes only.

Client Signup Tool
^^^^^^^^^^^^^^^^^^

The client signup tool can be used to sign up new clients to the mainstay service.

``go run $GOPATH/src/mainstay/cmd/clientsignuptool/clientsignuptool.go``

Connectivity to the mainstay db instance is required. Config can be set in ``cmd/clientsignuptool/conf.json``.

The client will need to provide an ECDSA public key. The corresponding private key will be used by the client to sign the commitment send to the mainstay API. The signature is then verified by the API using the public key provided.

The tool assigns a new position to the client in the commitment merkle tree and also provides a unique auth_token for authorizing API POST requests submitted by the client. For random auth-token generation only, token generator tool ``cmd/tokengeneratortool`` can be used.

For examples `check <../doc/signup.md>`_

Token Generator Tool
^^^^^^^^^^^^^^^^^^^^

The token generator tool can be used to generate unique authorization tokens for client signup.

``go run $GOPATH/src/mainstay/cmd/tokengeneratortool/tokengeneratortool.go``

Client Confirmation Tool
^^^^^^^^^^^^^^^^^^^^^^^^

The confirmation tool can be used to confirm all the attestations of a client Ocean-type sidechain to Bitcoin and wait for any new attestations that will be happening.

Running this tool will require a full Bitcoin testnet node and a full Ocean node. Connection details for these should be included in ``cmd/confirmationtool/conf.json``.

The ``API_HOST`` field should be set to the mainstay URL. This can be updated in ``cmd/confirmationtool/confirmationtool.go``.

To run this tool you need to first fetch the ``TX_HASH`` from the ``attestationhash`` field in the Ocean genesis block, as well as the publicly available ``REDEEM_SCRIPT`` of the attestation service multisig. The tool can also be started with any other ``TX_HASH`` attestation found in the mainstay website. A client should use his designated ``CLIENT_POSITION`` that was assigned during signup and run the tool using:

``go run cmd/confirmationtool/confirmationtool.go -tx TX_HASH -script REDEEM_SCRIPT -position CLIENT_POSITION -apiHost https://mainstay.xyz``

This will initially take some time to sync up all the attestations that have been committed so far and then will wait for any new attestations. Logging is displayed for each attestation and for full details the ``-detailed`` flag can be used.

Commitment Tool
^^^^^^^^^^^^^^^

The commitment tool can be used to send hash commitments to the Mainstay API.

The tool functions in three different modes:


* Init mode to generate ECDSA keys
* One time commitment mode
* Recurrent commitment of Ocean blockhashes mode

Various command line arguments need to be provided:


* `-apiHost`: host address of Mainstay API (default: https://mainstay.xyz)
* `-init`: init mode to generate ECDSA pubkey/privkey (default: false)
* `-ocean`: ocean mode to use recurrent commitment mode (default: false)
* `-delay`: delay in minutes between sending commitments in ocean mode (default: 60)
* ``-position``\ : client position on commitment merkle tree
* ``-authtoken``\ : client authorization token generated on registration
* ``-privkey``\ : Client private key, if signature has not been generated using a different source

Ocean connectivity details need to be provided in the ``cmd/commitmenttool/conf.json`` file if Ocean mode is selected.

For examples `check <../doc/commitment.md>`_

Multisig Tool
^^^^^^^^^^^^^

The multisig tool can be used to generate multisig scripts and P2SH addresses for Mainstay configuration.

Two modes:


* Regtest mode (multisig/P2SH generation for regtest and unit tests)
* Main mode

Command line arguments:


* ``-chain``\ : set bitcoin chain configuration to regtest/testnet/mainnet (defaults to mainnet)
* ``-nKeys``\ : num of keys (main mode)
* ``-nSigs``\ : num of sigs (main mode)
* ``-keys``\ : list of comma separated pub keys in hex format (main mode)
* ``-keysX``\ : list of comma separated pub key X coordinates (main mode if -keys not set)
* ``-keysY``\ : list of comma separated pub key Y coordinates (main mode if -keys not set)

The multisig generated can be used as the Mainstay ``initScript`` config option.

The P2SH address generated can be used to pay funds to initiate Mainstay.

Examples on how to run:


* ``go run $GOPATH/src/mainstay/cmd/multisigtool/multisigtool.go -chain=mainnet -nKeys=2 -nSigs=1 -keysX=17073944010873801765385810419928396464299027769026919728232198509972577863206,80413053216156218546514694130398099327511867032326801302280634421130221500147 -keysY=475813022329769762590164284448176075334749443379722569322944728779216384721,11222700187475866687235948284541357909717856537392660494591205788179681685365``
* ``go run $GOPATH/src/mainstay/cmd/multisigtool/multisigtool.go -chain=testnet -nKeys=2 -nSigs=1 -keys=03e52cf15e0a5cf6612314f077bb65cf9a6596b76c0fcb34b682f673a8314c7b33,03e52cf15e0a5cf6612314f077bb65cf9a6596b76c0fcb34b682f673a8314c7b33``
* ``go run $GOPATH/src/mainstay/cmd/multisigtool/multisigtool.go -chain=regtest``

For example use cases go to `docs </doc/>`_.


Initialising Mainstay
----------------------

A set of instructions for setting up the Mainstay service. This requires running 2 Bitcoin full nodes and setting up a 1 of 2 P2SH multisig address for the attestations. The Mainstay service coordinates with the transaction signing tools via zmq, sending attested hashes, new commitments and transactions to sign. All transactions are committted through the main service.

Alternatively HSM interfaces can be used instead of the transaction signing tools. In this case, the P2SH address is generated by combining the pubkeys of the HSMs instead. The rest of the functionality should work in a very similar manner.

Initial attestation
^^^^^^^^^^^^^^^^^^^

Generate 2 addresses
********************

::

   $ bitcoin-cli -datadir=testnetbtc-datadir/ getnewaddress
   2MwvCUjtecBAFcc7SWhEu8NyT1bLsCRtN6J

   $ bitcoin-cli -datadir=testnetbtc-datadir/ getnewaddress
   2N4FJ6xpbGdUvC8RjfMmQ6bzWXwEfWCFcYF

Generate multisig 1 of 2 address
********************************

::

   $ bitcoin-cli -datadir=testnetbtc-datadir/ addmultisigaddress 1 "[\"2NFBB5okotyGFLmceXK7q18ufuv11NmefUJ\",\"2NE8WKRRuj53udVsuyj5GbVfyUNN6ZSE4ia\"]" "" legacy

   {
     "address": "2N5ckx6eXY5vx3DLwBwSZsNVShiZ6k6mSGd",
     "redeemScript": "512103d11753d31309988c323142a0171e5b2319a8651479835afa4ab8ecb6442141b921034f0538871c910019b8e15a3b400b63971703822c9b2ab8b4183200e548fefbf952ae"
   }


Dump priv keys
**************

::

    $ bitcoin-cli -datadir=testnetbtc-datadir/ dumpprivkey 2NFBB5okotyGFLmceXK7q18ufuv11NmefUJ cTgsB8DjF2vjhFrtCPopvknmNWP6CTQeb1Sd9zvXxRF5qHp9V4ct

    $ bitcoin-cli -datadir=testnetbtc-datadir/ dumpprivkey 2NE8WKRRuj53udVsuyj5GbVfyUNN6ZSE4ia cNGEfurnx9oL6z8XUuigPXxoxs5cmMxfwwnDbAa258StQ4AQTH8P

Import generated multisig address
*********************************

::

    $ bitcoin-cli -datadir=testnetbtc-datadir/ importaddress 2N5ckx6eXY5vx3DLwBwSZsNVShiZ6k6mSGd "" false

Send funds to generated multisig address
****************************************

::

    $ bitcoin-cli -datadir=testnetbtc-datadir/ getbalance
    0.07926900

    bitcoin-cli -datadir=testnetbtc-datadir/ sendtoaddress 2N5ckx6eXY5vx3DLwBwSZsNVShiZ6k6mSGd 0.07926900 "" "" true

    87e56bda501ba6a022f12e178e9f1ac03fb2c07f04e1dfa62ac9e1d83cd840e1

    bitcoin-cli -datadir=testnetbtc-datadir/ sendrawtransaction 02000000000101bbd869ef95b3280aad7e6d8c77582d1d7a3d0dc60fc3c3c0228df6931c31561b000000002322002055dec27024dac08f4a4c0738c4834315e6c99004b02348b2f9df960926f44c4bfeffffff0158e878000000000017a9146d237e71ec246acfcc80b249e0e835b9bfe2175687030047304402200c972818b73932c6f48d86f9b9a2c1a67d42b6b798280e51e101145d247630ac022037984534b3e06d38eaecdc849c06d25b35d98330d96b95af8106897188b540050147512102e1ee4e5801efc577a8a9fac006a5908af7dfd37b03bd6bba830d1d3cb7a1ba7821027e73fcf0a3d86eaad56cae92524d4eeac42ec0e83af75c10b2d171f43f42325c52aed4051600
    87e56bda501ba6a022f12e178e9f1ac03fb2c07f04e1dfa62ac9e1d83cd840e1

Topup information
^^^^^^^^^^^^^^^^^

Generate 2 addresses
********************

::

    $ bitcoin-cli -datadir=/Users/nikolaos/testnetbtc-datadir2/ getnewaddress
    2MtEZ7J8ZXoieL7iHyUQw91TZpLEcVQTAyK

    $ bitcoin-cli -datadir=/Users/nikolaos/testnetbtc-datadir2/ getnewaddress
    2MwvCUjtecBAFcc7SWhEu8NyT1bLsCRtN6J

Generate multisig 1 of 2 address
********************************

::

    $ bitcoin-cli -datadir=/Users/nikolaos/testnetbtc-datadir2/ addmultisigaddress 1 "[\"2MtEZ7J8ZXoieL7iHyUQw91TZpLEcVQTAyK\",\"2MwvCUjtecBAFcc7SWhEu8NyT1bLsCRtN6J\"]" "" legacy

    {
      "address": "2NBYFyyMpPeLCb67bykLBHMByu1dSRGsim1",
      "redeemScript": "512102a2411030da6082ac32d0166fc19f03e264c6c2a138f83a29120d0b59969670792103d11753d31309988c323142a0171e5b2319a8651479835afa4ab8ecb6442141b952ae"
    }

Dump priv keys
**************

::

    $ bitcoin-cli -datadir=/Users/nikolaos/testnetbtc-datadir2/ dumpprivkey 2MtEZ7J8ZXoieL7iHyUQw91TZpLEcVQTAyK
    cPLfW9BRRJjZNwNHwrz6B5XEmsTHRFsHYYFRTAQChULT5nUn8FkW

    $ bitcoin-cli -datadir=/Users/nikolaos/testnetbtc-datadir2/ dumpprivkey 2MwvCUjtecBAFcc7SWhEu8NyT1bLsCRtN6J
    cTgsB8DjF2vjhFrtCPopvknmNWP6CTQeb1Sd9zvXxRF5qHp9V4ct

Running the service
-------------------

``go build && go install && mainstay``

Running the signing tools
^^^^^^^^^^^^^^^^^^^^^^^^^

* signer 1

``go run $GOPATH/src/mainstay/cmd/txsigningtool/txsigningtool.go -pk cTgsB8DjF2vjhFrtCPopvknmNWP6CTQeb1Sd9zvXxRF5qHp9V4ct -pkTopup cPLfW9BRRJjZNwNHwrz6B5XEmsTHRFsHYYFRTAQChULT5nUn8FkW -host *:5001``


* signer 2

``go run $GOPATH/src/mainstay/cmd/txsigningtool/txsigningtool.go -pk cNGEfurnx9oL6z8XUuigPXxoxs5cmMxfwwnDbAa258StQ4AQTH8P -pkTopup cTgsB8DjF2vjhFrtCPopvknmNWP6CTQeb1Sd9zvXxRF5qHp9V4ct -host *:5002``


Commitment examples
-------------------

Signing and Sending Commitments
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The commitment tool can be used to send signed commitments to the Mainstay API. The commitment is a 32 byte hash. While for a typical Ocean sidechain Client this will be the latest blockhash, any form of data can be hashed into this form. Various tools exist that can achieve this.

To use the commitment tool the client ``private key``\ , ``auth token`` and ``position`` assigned during signup are required, along with the 32 byte hash ``commitment`` to be signed and send.


* The commitment tool can be used one off to produce a signature for a commitment hash using the client's private key:

.. code-block::

   go run cmd/commitmenttool/commitmenttool.go
   ****************************
   ****** Commitment mode *****
   ****************************

   Insert commitment: 879d36232614b868a52549cf6961caaa4c8f09d3ecffb63714d40d6015f38229

   Sign commitment, send commitment or both? sign

   Insert private key: 15918e81d40354eca67b1538e8d3bd2672f8c0016cb36c12fb66042f9d839870

   Signature: MEQCICC8ngtdSaXRtrlWF163b+LJ+yxONBIGrFZfdtz843TzAiAyPh/8coCkv8iROeTohAD+yUg5i8Y+Plcx9ZAVobWsdQ==


* These data can then be submitted via the Mainstay website:


.. image:: images/commitment.png
   :target: images/commitment.png
   :alt: alt text



* Alternatively the commitment tool can be used directly to sign and send the commitment to the Mainstay API:

.. code-block::

   go run cmd/commitmenttool/commitmenttool.go
   ****************************
   ****** Commitment mode *****
   ****************************

   Insert commitment: 879d36232614b868a52549cf6961caaa4c8f09d3ecffb63714d40d6015f38229

   Sign commitment, send commitment or both? send

   Insert signature: MEQCICC8ngtdSaXRtrlWF163b+LJ+yxONBIGrFZfdtz843TzAiAyPh/8coCkv8iROeTohAD+yUg5i8Y+Plcx9ZAVobWsdQ==

   Insert position: 3

   Insert auth token: e0950c03-2f71-42b8-af5c-a564b26a9f97
   response Status: 200 OK
   Success!


* Or both in one go (or Go):

.. code-block::

   go run cmd/commitmenttool/commitmenttool.go
   ****************************
   ****** Commitment mode *****
   ****************************

   Insert commitment: 879d36232614b868a52549cf6961caaa4c8f09d3ecffb63714d40d6015f38229

   Sign commitment, send commitment or both? both

   Insert private key: 15918e81d40354eca67b1538e8d3bd2672f8c0016cb36c12fb66042f9d839870

   Signature: MEQCICC8ngtdSaXRtrlWF163b+LJ+yxONBIGrFZfdtz843TzAiAyPh/8coCkv8iROeTohAD+yUg5i8Y+Plcx9ZAVobWsdQ==

   Insert position: 3

   Insert auth token: e0950c03-2f71-42b8-af5c-a564b26a9f97
   response Status: 200 OK
   Success!

Key Init
********

The commitment tool can also be used to generate private/public ECDSA key pairs if run on ``init`` mode. This is displayed below:

.. code-block::

   $ go run cmd/commitmenttool/commitmenttool.go -init
   2019/01/30 12:29:54 Init mode
   2019/01/30 12:29:54 Generating new key...
   2019/01/30 12:29:54 generated priv: 15918e81d40354eca67b1538e8d3bd2672f8c0016cb36c12fb66042f9d839870
   2019/01/30 12:29:54 generated pub: 021805882d0939594b34f1c31e8d6d9cc19700c6e08cc9d83c267ae318ec52b796
   2019/01/30 12:29:54 The private key should be used for signing future client commitments
   2019/01/30 12:29:54 The public key should be provided when posting these to Mainstay API
   2019/01/30 12:29:54 Finishing...

Commitment Verification
^^^^^^^^^^^^^^^^^^^^^^^

To verify if a commitment has been included in an attestation the following API call can be used:

https://mainstay.xyz/api/v1/commitment/verify?position=3&commitment=879d36232614b868a52549cf6961caaa4c8f09d3ecffb63714d40d6015f38229

To get more detailed information on the commitment use:

https://mainstay.xyz/api/v1/commitment/commitment?commitment=879d36232614b868a52549cf6961caaa4c8f09d3ecffb63714d40d6015f38229

More information on the Mainstay API can be found on `github <https://github.com/commerceblock/mainstay-mvc/blob/develop/doc/mainstay_api.md>`_.



