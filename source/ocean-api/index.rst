Ocean API reference
===================

The Ocean client inherits all of the *Remote Procedure Calls* (RPCs) from *Elements 0.14* (described `here <https://github.com/ElementsProject/elementsbp-api-reference/blob/master/api.md>`_\) which in turn inherits all RPCs from *Bitcoin 0.13*. Ocean specific RPCs control the advanced and extended features unique to the platform.
This document describes these new RPCs and their function as well as additional Ocean client configuration options that enable them.

For any RPC supported in the Ocean platform client (including those inherited from Elements and
Bitcoin), you can get information about function and correct usage from the command line
using the ``help`` RPC.  For example,

.. code-block:: text

   ocean-cli help getblockchaininfo

As in Elements and Bitcoin, the Configuration options can be passed to oceand as command line arguments or added to the ``ocean.conf`` configuration file. For a full list of RPCs and configuration options use ``oceand -help``.

Quick reference
---------------

The following RPCs are unique to the Ocean client

Wallet
^^^^^^

* `dumpderivedkeys`_
* `validatederivedkeys`_
* `dumpkycfile`_
* `readkycfile`_
* `createkycfile`_
* `getderivedkeys`_
* `getcontract`_
* `getcontracthash`_
* `getmappinghash`_
* `getethaddress`_
* `getethpeginaddress`_
* `getethpegin`_
* `createrawethpegin`_
* `validateethpegin`_
* `claimethpegin`_
* `sendtoethmainchain`_
* `sendanytoaddress`_
* `createanytoaddress`_


Utility
^^^^^^^

* `getutxoassetinfo`_
* `createrawissuance`_
* `createrawreissuance`_
* `createrawburn`_
* `testmempoolaccept`_
* `createrawpolicytx`_
* `createrawrequesttx`_
* `getrequests`_
* `createrawbidtx`_
* `getrequestbids`_


Policy
^^^^^^

* `addtowhitelist`_
* `addmultitowhitelist`_
* `readwhitelist`_
* `querywhitelist`_
* `removefromwhitelist`_
* `clearwhitelist`_
* `dumpwhitelist`_
* `sendaddtowhitelisttx`_
* `sendaddmultitowhitelisttx`_
* `addtofreezelist`_
* `queryfreezelist`_
* `removefromfreezelist`_
* `clearfreezelist`_
* `addtoburnlist`_
* `queryburnlist`_
* `removefromburnlist`_
* `clearburnlist`_


Configuration options
^^^^^^^^^^^^^^^^^^^^^

* `pkhwhitelist`_
* `freezelist`_
* `burnlist`_
* `issuanceblock`_
* `disablect`_
* `embedcontract`_
* `attestationhash`_
* `embedmapping`_
* `issuecontrolscript`_
* `policycoins`_
* `initialfreecoinsdestination`_
* `freezelistcoinsdestination`_
* `burnlistcoinsdestination`_
* `issuancecoinsdestination`_
* `permissioncoinsdestination`_
* `mainchainrpchost`_
* `mainchainrpcport`_
* `validatepegin`_
* `parentgenesisblockhash`_
* `parentcontract`_
* `fedpegaddress`_
* `peginconfirmationdepth`_


dumpderivedkeys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dumpderivedkeys`` RPC outputs a list of all contract tweaked
addresses in the key pool
along with the corresponding non-tweaked basis public keys to a specified file.

*Parameter #1---the filename of the output file*

+----------+--------+---------------------+------------------------------------------------------------------------------------+
| Name     | Type   | Presence            | Description                                                                        |
+----------+--------+---------------------+------------------------------------------------------------------------------------+
| filename | String | Required(exactly 1) | The name of the output file for the list of tweaked addresses and base public keys |
+----------+--------+---------------------+------------------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli dumpderivedkeys dumpfile.txt



validatederivedkeys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``validatederivedkeys`` RPC reads in a list of tweaked addresses with corresponding
base public keys (as produced by `dumpderivedkeys <#dumpderivedkeys>`_\ ) from a specified file, and
then checks that the address corresponds to the corresponding public key when
tweaked with the current contract hash.

*Parameter #1---the filename of the input file*

+----------+--------+---------------------+------------------------------------------------------------------------------------+
| Name     | Type   | Presence            | Description                                                                        |
+----------+--------+---------------------+------------------------------------------------------------------------------------+
| filename | String | Required(exactly 1) | The name of the output file for the list of tweaked addresses and base public keys |
+----------+--------+---------------------+------------------------------------------------------------------------------------+


*Result---nothing if valid keys, RPC errors if invalid keys found*

*Example*

.. code-block:: text

   ocean-cli validatederivedkeys



dumpkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dumpkycfile`` RPC outputs an encrypted list of wallet tweaked public keys. A timestamp and best block hash of when the file was constructed is included.

*Parameter #1---the filename of the output file*

+----------+--------+---------------------+---------------------------------------------------------------------------+
| Name     | Type   | Presence            | Description                                                               |
+----------+--------+---------------------+---------------------------------------------------------------------------+
| filename | String | Required(exactly 1) | The name of the output file for the encrypted list of tweaked public keys |
+----------+--------+---------------------+---------------------------------------------------------------------------+


*Parameter #2---the public key with which to encrypt*

+---------------+-------------+-------------------+--------------------------------------------------------+
| Name          | Type        | Presence          | Description                                            |
+---------------+-------------+-------------------+--------------------------------------------------------+
| onboardpubkey | String(hex) | Optional (0 or 1) | The specific public key to be used for file encryption |
+---------------+-------------+-------------------+--------------------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli dumpkycfile dumpfile.txt 1CDXUtbF3bBtritydFMKhRbbYhxDgCF5oH



readkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``readkycfile`` RPC reads in an encrypted list of tweaked public keys (as produced by `dumpkycfile <#dumpkycfile>`_\) from a specified file, and outputs the unencrypted list of tweaked addresses along with their corresponding non-tweaked public keys.

*Parameter #1---the encrypted filename*

+----------+--------+---------------------+---------------------------------------------------------------------------+
| Name     | Type   | Presence            | Description                                                               |
+----------+--------+---------------------+---------------------------------------------------------------------------+
| filename | String | Required(exactly 1) | The name of the output file for the encrypted list of tweaked public keys |
+----------+--------+---------------------+---------------------------------------------------------------------------+


*Parameter #2---the filename of the output file*

+-------------+--------+---------------------+--------------------------------------------------------------------------------------------+
| Name        | Type   | Presence            | Description                                                                                |
+-------------+--------+---------------------+--------------------------------------------------------------------------------------------+
| outfilename | String | Required(exactly 1) | The name of the output file for the list of tweaked public keys and coresponding addresses |
+-------------+--------+---------------------+--------------------------------------------------------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli readkycfile dumpfile.txt dumpfileout.txt



createkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createkycfile`` RPC creates an encrypted kyc file that stores tweaked public key p2pkh and multisig address p2sh data to be whitelisted when onboarding.

*Parameter #1---the created KYC file name*

+-------------+--------+---------------------+----------------------+
| Name        | Type   | Presence            | Description          |
+-------------+--------+---------------------+----------------------+
| outfilename | String | Required(exactly 1) | Name of the KYC file |
+-------------+--------+---------------------+----------------------+


*Parameter #2---P2PKH data for whitelisting in an onboarding transaction*

+------------+----------------------------+----------------------+-----------------------------------------------------------------+
| Name       | Type                       | Presence             | Description                                                     |
+------------+----------------------------+----------------------+-----------------------------------------------------------------+
| pubkeylist | JSON array of JSON Objects | Required (1 or more) | Contains tweaked addresses and respective untweaked public keys |
+------------+----------------------------+----------------------+-----------------------------------------------------------------+


*Parameter #3---P2SH data for whitelisting in an onboarding transaction*

+--------------+----------------------------+----------------------+------------------------------------------------------------------------------------------------------+
| Name         | Type                       | Presence             | Description                                                                                          |
+--------------+----------------------------+----------------------+------------------------------------------------------------------------------------------------------+
| multisiglist | JSON array of JSON Objects | Required (1 or more) | Contains multisig metadata such as number of required signatures and arrays of untweaked public keys |
+--------------+----------------------------+----------------------+------------------------------------------------------------------------------------------------------+


*Parameter #4---the public key issued by the server for encryption.*

+---------------+-------------+----------------------+---------------------------------------------------+
| Name          | Type        | Presence             | Description                                       |
+---------------+-------------+----------------------+---------------------------------------------------+
| onboardpubkey | String(hex) | Optional (exactly 1) | Public key that is used for onboarding encryption |
+---------------+-------------+----------------------+---------------------------------------------------+

*Result---onboarding user public key if successful, null or rpc errors if passed data is invalid or wallet is not available*

*Example*

.. code-block:: bash

   ocean-cli createkycfile test [{"address":2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz,"pubkey":028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58}] [{"nmultisig":1,"pubkeys":[028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58,0263a73eca5334af77037a1c8844b5220017bf6fb627c5a57c862dff20ea001d99]}]

Result:

.. code-block:: text

   028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58



getderivedkeys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getderivedkeys`` RPC returns a list of contract tweaked
addresses in the key pool
along with the corresponding non-tweaked basis public keys as a JSON object.

*Parameters: none*

*Result---the txid and vout of the reissuance output*

+-----------+----------------------+-------------------------+------------------------------------------------------------------------------+
| Name      | Type                 | Presence                | Description                                                                  |
+-----------+----------------------+-------------------------+------------------------------------------------------------------------------+
| result    | Objects              | Required (exactly 1)    | An object containing a list of contract tweaked addresses and                |
|           |                      |                         | basis public keys                                                            |
+-----------+----------------------+-------------------------+------------------------------------------------------------------------------+
|           | String (base58check) | Required(key pool size) | Base58check encoded address corresponding to the contract tweaked public key |
| →         |                      |                         |                                                                              |
| `address` |                      |                         |                                                                              |
+-----------+----------------------+-------------------------+------------------------------------------------------------------------------+
|           | String (hex)         | Required(key pool size) | Hex encoding of the compressed untweaked public key                          |
| →         |                      |                         |                                                                              |
| `bpubkey` |                      |                         |                                                                              |
+-----------+----------------------+-------------------------+------------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getderivedkeys

Result:

.. code-block:: json

   {
     "address": ["2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz","2daBDLApGapXjW4xErMsYDAHWd2QzFHHxvB"],
     "bpubkey": ["028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58","0263a73eca5334af77037a1c8844b5220017bf6fb627c5a57c862dff20ea001d99"]
   }



getcontract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getcontract`` RPC returns the plain text of the currently enforced
contract.

*Parameters: none*

*Result---the full plain text of the current contract*

+----------+---------+----------------------+---------------------------------------------------------+
| Name     | Type    | Presence             | Description                                             |
+----------+---------+----------------------+---------------------------------------------------------+
| contract | Objects | Required (exactly 1) | A JSON object containing the plain text of the contract |
+----------+---------+----------------------+---------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getcontract

Result:

.. code-block:: json

   {
     "contract": "These are the current terms and conditions that govern participation in the Ocean network. 1. Be awesome to each other. 2. No smoking."
   }



getcontracthash
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getcontracthash`` RPC returns the hash of the contract in force at a given block height.
If the block height is not supplied, the current contract hash is returned.

*Parameter #1---the blockheight at which a contract was in force*

+-------------+---------+------------------+------------------------------------------------------------------------------+
| Name        | Type    | Presence         | Description                                                                  |
+-------------+---------+------------------+------------------------------------------------------------------------------+
| blockheight | Integer | Optional(0 or 1) | Block height to retrieve the contract hash from (Default: most recent block) |
+-------------+---------+------------------+------------------------------------------------------------------------------+


*Result---the contract hash*

+--------------+--------+-------------------------------------------+
| Name         | Type   | Description                               |
+--------------+--------+-------------------------------------------+
| contracthash | String | The hex-encoded hash of the contract hash |
+--------------+--------+-------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getcontracthash

Result:

.. code-block:: text

   f4f30db53238a7529bc51fcda04ea22bd8f8b188622a6488da12281874b71f72



getmappinghash
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getmappinghash`` RPC returns the hash of the mapping object in force at a given block height.
If the block height is not supplied, the current mapping hash is returned.

*Parameter #1---the blockheight at which a mapping was in force*

+-------------+---------+------------------+------------------------------------------------------------------------------+
| Name        | Type    | Presence         | Description                                                                  |
+-------------+---------+------------------+------------------------------------------------------------------------------+
| blockheight | Integer | Optional(0 or 1) | Block height to retrieve the contract hash from (Default: most recent block) |
+-------------+---------+------------------+------------------------------------------------------------------------------+



*Result---the mapping hash*

+---------+--------+-------------------------------------------+
| Name    | Type   | Description                               |
+---------+--------+-------------------------------------------+
| mapping | String | The hex-encoded hash of the contract hash |
+---------+--------+-------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getmappinghash

Result:

.. code-block:: text

   f4f30db53238a7529bc51fcda04ea22bd8f8b188622a6488da12281874b71f72



getethaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getethaddress`` RPC returns an ethereum address from an EC private key.

*Parameter #1 ---EC private key*

+------+--------------+----------------------+-----------------------------------------------+
| Name | Type         | Presence             | Description                                   |
+------+--------------+----------------------+-----------------------------------------------+
| key  | String (hex) | Required (exactly 1) | private key to generate Ethereum address from |
+------+--------------+----------------------+-----------------------------------------------+

*Result---ethereum address if sucessful, RPC error if invalid data private key given*

+---------+----------------------+----------------------------------------+
| Name    | Type                 | Description                            |
+---------+----------------------+----------------------------------------+
| address | String (base58check) | Ethereum address for given private key |
+---------+----------------------+----------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getethaddress 3ecb44df2159c26e0f995712d4f39b6f6e499b40749b1cf1246c37f9516cb6a4

Result:

.. code-block:: text

   8a40bfaa73256b60764c1bf40675a99083efb075



getethpeginaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getethpeginaddress`` RPC returns information needed for claimethpegin to move coins to the sidechain.
The user should send CBT coins from their eth wallet to the eth_mainchain_address returned. The user needs
to provide their eth priv key, which is used to generate a claim pubkey that is added to the pegin transaction.
The transaction is then signed with the key provided.

IMPORTANT: getethpeginaddress adds new secrets to wallet.dat, necessitating backup on a regular basis.

*Parameter #1 --- private key*

+------+--------------+----------------------+--------------------------------------------------------------------+
| Name | Type         | Presence             | Description                                                        |
+------+--------------+----------------------+--------------------------------------------------------------------+
| key  | String (hex) | Required (exactly 1) | private key to generate Ethereum address and claim public key from |
+------+--------------+----------------------+--------------------------------------------------------------------+


*Result---JSON object containing Ethereum address and corresponding public key, or RPC error if unvalid private key given*

+------------------------+--------------+----------------------------------------+
| Name                   | Type         | Description                            |
+------------------------+--------------+----------------------------------------+
| eth_mainchain_addresss | String (hex) | Ethereum address for given private key |
+------------------------+--------------+----------------------------------------+
| eth_claim_pubkey       | String (hex) | Claim Public key for given private key |
+------------------------+--------------+----------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getethpeginaddress 3ecb44df2159c26e0f995712d4f39b6f6e499b40749b1cf1246c37f9516cb6a4

Result:

.. code-block:: json

    {
      "eth_mainchain_address": "b6872561de5ba19d38071a7616d9d434b9e37860",
      "eth_claim_pubkey": "0397466f2b32bc3bb76d4741ae51cd1d8578b48d3f1e68da206d47321aec267ce7"
    }



getethpegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getethpegin`` RPC returns an eth ERC-20 peg-in transaction via geth rpc connectivity.

*Parameter #1 --- eth transaction id*

+------+--------------+----------------------+--------------------------------+
| Name | Type         | Presence             | Description                    |
+------+--------------+----------------------+--------------------------------+
| txid | String (hex) | Required (exactly 1) | Ethereum peg-in transaction id |
+------+--------------+----------------------+--------------------------------+


*Result---the transaction in JSON format, or RPC error if given transaction is invalid*

+--------+------+---------------------------+
| Name   | Type | Description               |
+--------+------+---------------------------+
| result | JSON | The resulting transaction |
+--------+------+---------------------------+


*Example*

.. code-block:: bash

   ocean-cli getethpegin 8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7

Result:

.. code-block:: json

    {
      "blockHash": "0x39f2086a66c17a568c9b6e6de8eb5f972f850aa9f282325c4c8d6471ac43182c",
      "blockNumber": "0x48e23c",
      "contractAddress": null,
      "cumulativeGasUsed": "0x1d6c50",
      "from": "0x8027af3c8ab3a7b603873cf17f521d049d615862",
      "gasUsed": "0xca30",
      "logs": [
        {
          "address": "0x076c97e1c869072ee22f8c91978c99b4bcb02591",
          "topics": [
            "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
            "0x0000000000000000000000008027af3c8ab3a7b603873cf17f521d049d615862",
            "0x0000000000000000000000002e6e830d10fb4f4f4211e84ae5d90083328684cd"
          ],
          "data": "0x000000000000000000000000000000000000000000df56b9541c229fce000000",
          "blockNumber": "0x48e23c",
          "transactionHash": "0x8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7",
          "transactionIndex": "0x4b",
          "blockHash": "0x39f2086a66c17a568c9b6e6de8eb5f972f850aa9f282325c4c8d6471ac43182c",
          "logIndex": "0xf",
          "removed": false
        }
      ],
      "logsBloom": "0x00000000000000000000080000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002010000000000000000000000000000000000000000000000000000000000000000000000000000000020000000000000000200000000000000000000000000000000000000004000002000000000000000000020000000000000000000000000000000000000000000000000000000000040000000040000000000000000080000000000000",
      "status": "0x1",
      "to": "0x076c97e1c869072ee22f8c91978c99b4bcb02591",
      "transactionHash": "0x8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7",
      "transactionIndex": "0x4b"
    }



createrawethpegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawethpegin`` RPC creates a raw CBT peg-in from an eth ERC-20 transaction.


*Parameter #1 --- eth transaction id*

+------+--------------+----------------------+--------------------------------+
| Name | Type         | Presence             | Description                    |
+------+--------------+----------------------+--------------------------------+
| txid | String (hex) | Required (exactly 1) | Ethereum peg-in transaction id |
+------+--------------+----------------------+--------------------------------+


*Parameter #2 --- eth transaction peg-in amount*

+--------+--------+----------------------+------------------------------------+
| Name   | Type   | Presence             | Description                        |
+--------+--------+----------------------+------------------------------------+
| amount | Amount | Required (exactly 1) | Ethereum peg-in transaction amount |
+--------+--------+----------------------+------------------------------------+


*Parameter #3 --- claim pubkey generated by getethpeginaddress*

+--------------+--------------+----------------------+------------------+
| Name         | Type         | Presence             | Description      |
+--------------+--------------+----------------------+------------------+
| claim_pubkey | String (hex) | Required (exactly 1) | Claim public key |
+--------------+--------------+----------------------+------------------+


*Result---ethereum address, or RPC error if invalid claim public key given*

+---------+--------------+----------------------------------------+
| Name    | Type         | Description                            |
+---------+--------------+----------------------------------------+
| address | String (hex) | Ethereum address for given private key |
+---------+--------------+----------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli createrawethpegin 8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7 432.109 03220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a

Result:

.. code-block:: text

    0200000001016ca60fb08c36a2e77e0810de32181b63e8250fbf9a398f9bf9e53444cbf680300000004000ffffffff0201bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a01000003a3529429a8001976a914923e4d01599a5f200ad0527dc046e85bc4d19a5888ac01bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a010000000000001658000000000000000000050800409452a303000020bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a20a38fcbb10d8cec9ae6346a90d018a14567f5d5406ab810c0f8ae76f84067e5d42103220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a206ca60fb08c36a2e77e0810de32181b63e8250fbf9a398f9bf9e53444cbf6803000000000



validateethpegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``validateethpegin`` RPC validates an eth ERC-20 transaction to be used from peg-in to Ocean.

Configuation options ``validatepegin=1``, ``mainchainrpchost=$GETH_RPC_HOST`` and ``mainchainrpcport=$GETH_RPC_PORT`` must be set for this command.


*Parameter #1 --- eth transaction id*

+------+--------------+----------------------+--------------------------------+
| Name | Type         | Presence             | Description                    |
+------+--------------+----------------------+--------------------------------+
| txid | String (hex) | Required (exactly 1) | Ethereum peg-in transaction id |
+------+--------------+----------------------+--------------------------------+


*Parameter #2 --- eth transaction peg-in amount*

+--------+--------+----------------------+------------------------------------+
| Name   | Type   | Presence             | Description                        |
+--------+--------+----------------------+------------------------------------+
| amount | Amount | Required (exactly 1) | Ethereum peg-in transaction amount |
+--------+--------+----------------------+------------------------------------+


*Parameter #3 --- claim pubkey generated by getethpeginaddress*

+--------------+--------------+----------------------+------------------+
| Name         | Type         | Presence             | Description      |
+--------------+--------------+----------------------+------------------+
| claim_pubkey | String (hex) | Required (exactly 1) | Claim public key |
+--------------+--------------+----------------------+------------------+


*Result---TRUE or FALSE*

+--------+------+-------------------+
| Name   | Type | Description       |
+--------+------+-------------------+
| result | Bool | validation result |
+--------+------+-------------------+

*Example*

.. code-block:: bash

   ocean-cli validateethpegin 8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7 432.109 03220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a

Result:

.. code-block:: text

    true



claimethpegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``claimethpegin`` RPC claims ERC-20 CBT tokens from eth to Ocean.


*Parameter #1 --- eth transaction id*

+------+--------------+----------------------+--------------------------------+
| Name | Type         | Presence             | Description                    |
+------+--------------+----------------------+--------------------------------+
| txid | String (hex) | Required (exactly 1) | Ethereum peg-in transaction id |
+------+--------------+----------------------+--------------------------------+


*Parameter #2 --- eth transaction peg-in amount*

+--------+--------+----------------------+------------------------------------+
| Name   | Type   | Presence             | Description                        |
+--------+--------+----------------------+------------------------------------+
| amount | Amount | Required (exactly 1) | Ethereum peg-in transaction amount |
+--------+--------+----------------------+------------------------------------+


*Parameter #3 --- claim pubkey generated by getethpeginaddress*

+--------------+--------------+----------------------+------------------+
| Name         | Type         | Presence             | Description      |
+--------------+--------------+----------------------+------------------+
| claim_pubkey | String (hex) | Required (exactly 1) | Claim public key |
+--------------+--------------+----------------------+------------------+


*Result---ethereum transaction ID, or RPC error if invalid inputs given*

+------+--------------+-------------------------+
| Name | Type         | Description             |
+------+--------------+-------------------------+
| txid | String (hex) | Ethereum transaction ID |
+------+--------------+-------------------------+


*Example*

.. code-block:: bash

   ocean-cli claimethpegin 8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7 432.109 03220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a

Result:

.. code-block:: text

    bb2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15



sendtoethmainchain
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendtoethmainchain`` RPC sends sidechain funds to the given eth mainchain address via federated peg-out.


*Parameter #1 --- destination address on eth mainchain*

+---------+--------------+----------------------+------------------------------------------------+
| Name    | Type         | Presence             | Description                                    |
+---------+--------------+----------------------+------------------------------------------------+
| address | String (hex) | Required (exactly 1) | Destination Ethereum address for funds         |
+---------+--------------+----------------------+------------------------------------------------+


*Parameter #2 --- eth amount pegged-out to eth mainchain*

+--------+--------+----------------------+-------------------------+
| Name   | Type   | Presence             | Description             |
+--------+--------+----------------------+-------------------------+
| amount | Amount | Required (exactly 1) | Ethereum peg-out amount |
+--------+--------+----------------------+-------------------------+


*Parameter #3 --- Fee deducted from amount being pegged-out*

+-----------------------+---------+-------------------+-------------------------------------------+
| Name                  | Type    | Presence          | Description                               |
+-----------------------+---------+-------------------+-------------------------------------------+
| subtractfeefromamount | Boolean | Optional (0 or 1) | Whether to deduct fee from peg-out amount |
+-----------------------+---------+-------------------+-------------------------------------------+


*Result---ethereum transaction ID of resulting sidechain transaction, or RPC error if invalid inputs given*

+------+--------------+-------------------------+
| Name | Type         | Description             |
+------+--------------+-------------------------+
| txid | String (hex) | Ethereum transaction ID |
+------+--------------+-------------------------+


*Example*

.. code-block:: bash

   ocean-cli sendtoethmainchain 8e8a0ec05cc3c2b8511aabadeeb821df19ea7564 533.22 false

Result:

.. code-block:: text

    aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15



sendanytoaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendanytoaddress`` RPC sends a combination of any non-policy assets to a given address. The cumulative sum of the assets is equal to the desired amount. This rpc should only be used in chains that are comprised of non-policy assets which are fungible.


*Parameter #1 --- destination address*

+---------+----------------------+----------------------+----------------------+
| Name    | Type                 | Presence             | Description          |
+---------+----------------------+----------------------+----------------------+
| address | String (base58check) | Required (exactly 1) | Destination  address |
+---------+----------------------+----------------------+----------------------+


*Parameter #2 --- amount to be sent to the destination*

+--------+--------+----------------------+-------------------------+
| Name   | Type   | Presence             | Description             |
+--------+--------+----------------------+-------------------------+
| amount | Amount | Required (exactly 1) | Total amount to be send |
+--------+--------+----------------------+-------------------------+


*Parameter #3 --- A comment used to store what the transaction is for*

+---------+--------+-------------------+--------------------------------------+
| Name    | Type   | Presence          | Description                          |
+---------+--------+-------------------+--------------------------------------+
| comment | String | Optional (0 or 1) | Comment to store transaction purpose |
+---------+--------+-------------------+--------------------------------------+


*Parameter #4 --- (string, optional) A comment to store the name of the person or organization to which you're sending the transaction*

+------------+--------+-------------------+------------------------------------+
| Name       | Type   | Presence          | Description                        |
+------------+--------+-------------------+------------------------------------+
| comment_to | String | Optional (0 or 1) | Comment to store name of recipient |
+------------+--------+-------------------+------------------------------------+


*Parameter #5 --- Return a transaction even when a blinding attempt fails due to number of blinded inputs/outputs if this is set to true*

+-----------------+---------+-------------------+-------------------------------------------------------------+
| Name            | Type    | Presence          | Description                                                 |
+-----------------+---------+-------------------+-------------------------------------------------------------+
| ignoreblindfail | Boolean | Optional (0 or 1) | If true return a transaction even if blinding attempt fails |
+-----------------+---------+-------------------+-------------------------------------------------------------+


*Parameter #6 --- Split a transaction that goes over the size limit into smaller transactions if this is set to true*

+---------------+---------+-------------------+-----------------------------------------------------------+
| Name          | Type    | Presence          | Description                                               |
+---------------+---------+-------------------+-----------------------------------------------------------+
| splitlargetxs | Boolean | Optional (0 or 1) | If true split a transaction that goes over the size limit |
+---------------+---------+-------------------+-----------------------------------------------------------+


*Parameter #7 --- Choose which balances should be used first. 1 - descending, 2 - ascending*

+-----------------+---------+-------------------+------------------------------------+
| Name            | Type    | Presence          | Description                        |
+-----------------+---------+-------------------+------------------------------------+
| balanceSortType | Integer | Optional (0 or 1) | Specify which balance to use first |
+-----------------+---------+-------------------+------------------------------------+


*Result---txid of generated transaction, or RPC error if failure*

+------+--------------+------------------------------+
| Name | Type         | Description                  |
+------+--------------+------------------------------+
| txid | String (hex) | Constructed transaction txid |
+------+--------------+------------------------------+


*Example*

.. code-block:: bash

   ocean-cli sendtanytoaddress 8e8a0ec05cc3c2b8511aabadeeb821df19ea7564 533.22

Result:

.. code-block:: text

    aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15


createanytoaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createanytoaddress`` RPC creates a transaction that sends an amount to a given address with as many non-policy assets as needed. This rpc should only used in chains that are comprised of non-policy assets which are fungible.


*Parameter #1 --- destination address*

+---------+----------------------+----------------------+----------------------+
| Name    | Type                 | Presence             | Description          |
+---------+----------------------+----------------------+----------------------+
| address | String (base58check) | Required (exactly 1) | Destination  address |
+---------+----------------------+----------------------+----------------------+



*Parameter #2 --- amount to be sent to the destination*

+--------+--------+----------------------+-------------------+
| Name   | Type   | Presence             | Description       |
+--------+--------+----------------------+-------------------+
| amount | Amount | Required (exactly 1) | Amount to be send |
+--------+--------+----------------------+-------------------+


*Parameter #3 --- Return a transaction even when a blinding attempt fails due to number of blinded inputs/outputs if this is set to true*

+-----------------+---------+-------------------+-------------------------------------------------------------+
| Name            | Type    | Presence          | Description                                                 |
+-----------------+---------+-------------------+-------------------------------------------------------------+
| ignoreblindfail | Boolean | Optional (0 or 1) | If true return a transaction even if blinding attempt fails |
+-----------------+---------+-------------------+-------------------------------------------------------------+


*Parameter #4 --- Split a transaction that goes over the size limit into smaller transactions if this is set to true*

+---------------+---------+-------------------+-----------------------------------------------------------+
| Name          | Type    | Presence          | Description                                               |
+---------------+---------+-------------------+-----------------------------------------------------------+
| splitlargetxs | Boolean | Optional (0 or 1) | If true split a transaction that goes over the size limit |
+---------------+---------+-------------------+-----------------------------------------------------------+


*Parameter #5 --- Choose which balances should be used first. 1 - descending, 2 - ascending*

+-----------------+---------+-------------------+------------------------------------+
| Name            | Type    | Presence          | Description                        |
+-----------------+---------+-------------------+------------------------------------+
| balanceSortType | Integer | Optional (0 or 1) | Specify which balance to use first |
+-----------------+---------+-------------------+------------------------------------+


*Parameter #6 --- Allow the selection of watch only inputs similar to fundrawtransaction*

+----------------+---------+-------------------+---------------------------------+
| Name           | Type    | Presence          | Description                     |
+----------------+---------+-------------------+---------------------------------+
| allowwatchonly | Boolean | Optional (0 or 1) | Allow watch only outputs or not |
+----------------+---------+-------------------+---------------------------------+


*Result---array raw generated transactions, or RPC error if failure*

+------+--------------------+------------------+
| Name | Type               | Description      |
+------+--------------------+------------------+
| txs  | Array (hex string) | Raw transactions |
+------+--------------------+------------------+


*Example*

.. code-block:: bash

   ocean-cli createanytoaddress 2djSpCPTpqFVmES6CLGhSYWFcq6ZpcecVNN 9 True True 2 True

Result:

.. code-block:: text

    ['020000000002f39a588792670f24b2311474796d3b7ba764f080a7b5ce01ee789adeaf93f3fa0000000000feffffffae41e81f92174d7c5182781515ef84e012628ceaaa6af6a4256504c928fcff440000000000feffffff0301bcdcf7ae294c68f8a0cc5adff210591656e2900acb6a02afe078232174a0a170010000000035a4e900001976a9146de4da462cd54703be66657ab8e11b010abc089988ac01aa8b62c60e78b5457747ff06a6eb14d9a32075d7657bb9723d00deee73e35095010000000035a4bcb4001976a914617f3f9964cb82a7fa67ad84cc3011031c0f322688ac01aa8b62c60e78b5457747ff06a6eb14d9a32075d7657bb9723d00deee73e35095010000000000002c4c00004a000000']



getutxoassetinfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getutxoassetinfo`` RPC returns a summary of the total amounts of unspent (and un-burnt)
assets in the UTXO set. Amounts in transactions marked as frozen (i.e. with one output
having a zero address) are listed in a separate field.

*Parameters: none*

*Result---an array of JSON objects containing the unspent amounts for each issued asset*

+-------------------+--------------+-------------------------------------------------------------------+
| Name              | Type         | Description                                                       |
+-------------------+--------------+-------------------------------------------------------------------+
| contract          | Object       | An array of JSON objects of unspent amounts for each issued asset |
+-------------------+--------------+-------------------------------------------------------------------+
| →                 | String (hex) | Asset ID                                                          |
| `asset`           |              |                                                                   |
+-------------------+--------------+-------------------------------------------------------------------+
| →                 | Integer      | Number of spendable utxos for the asset                           |
| `spendabletxouts` |              |                                                                   |
+-------------------+--------------+-------------------------------------------------------------------+
| →                 | Amount       | Total amount of the asset that is spendable                       |
| `amountspendable` |              |                                                                   |
+-------------------+--------------+-------------------------------------------------------------------+
| →                 | Integer      | The number of the assets outputs that are frozen                  |
| `frozentxouts`    |              |                                                                   |
+-------------------+--------------+-------------------------------------------------------------------+
| →                 | Amount       | Total amount of the asset that is frozen                          |
| `amountfrozen`    |              |                                                                   |
+-------------------+--------------+-------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getutxoassetinfo

Result:

.. code-block:: json

   [
     {
       "asset": "7f7c00ca515e46165ea6a13dd49d22759beeb26a952128f1a5af824d208a051e",
       "spendabletxouts": 1,
       "amountspendable": 3.00000000,
       "frozentxouts": 0,
       "amountfrozen": 0.00000000
     },
     {
       "asset": "b2e15d0d7a0c94e4e2ce0fe6e8691b9e451377f6e46e8045a86f7c4b5d4f0f23",
       "spendabletxouts": 107,
       "amountspendable": 500000.00000000,
       "frozentxouts": 0,
       "amountfrozen": 0.00000000
     }
   ]



createrawissuance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawissuance`` RPC creates an unblinded raw unsigned issuance transaction with specified
outputs and spending from a specified input containing an amount of policy asset.

*Parameter #1---the Base58check address for the issued asset*

+--------------+----------------------+----------------------+----------------------------------+
| Name         | Type                 | Presence             | Description                      |
+--------------+----------------------+----------------------+----------------------------------+
| assetaddress | String (base58check) | Required (exactly 1) | Base58check issued asset address |
+--------------+----------------------+----------------------+----------------------------------+


*Parameter #2---the amount of issued asset*

+-------------+--------+----------------------+--------------------------+
| Name        | Type   | Presence             | Description              |
+-------------+--------+----------------------+--------------------------+
| assetamount | Amount | Required (exactly 1) | Amount of asset to issue |
+-------------+--------+----------------------+--------------------------+


*Parameter #3---the Base58check address for the reissuance token*

+--------------+----------------------+----------------------+--------------------------------------+
| Name         | Type                 | Presence             | Description                          |
+--------------+----------------------+----------------------+--------------------------------------+
| tokenaddress | String (base58check) | Required (exactly 1) | Base58check reissuance token address |
+--------------+----------------------+----------------------+--------------------------------------+


*Parameter #4---the amount of reissuance token*

+-------------+--------+----------------------+----------------------------------------+
| Name        | Type   | Presence             | Description                            |
+-------------+--------+----------------------+----------------------------------------+
| tokenamount | Amount | Required (exactly 1) | Amount of reissuance token to generate |
+-------------+--------+----------------------+----------------------------------------+


*Parameter #5---the Base58check address for the issuanceAsset change*

+---------------+----------------------+----------------------+----------------------------+
| Name          | Type                 | Presence             | Description                |
+---------------+----------------------+----------------------+----------------------------+
| changeaddress | String (base58check) | Required (exactly 1) | Base58check change address |
+---------------+----------------------+----------------------+----------------------------+


*Parameter #6---the amount of issuanceAsset change*

+--------------+--------+----------------------+------------------------------------+
| Name         | Type   | Presence             | Description                        |
+--------------+--------+----------------------+------------------------------------+
| changeamount | Amount | Required (exactly 1) | Amount of issuance asset to return |
+--------------+--------+----------------------+------------------------------------+


*Parameter #7---the number of issuanceAsset outputs*

+-----------+---------+----------------------+----------------------------------+
| Name      | Type    | Presence             | Description                      |
+-----------+---------+----------------------+----------------------------------+
| changenum | Integer | Required (exactly 1) | number of issuance asset outputs |
+-----------+---------+----------------------+----------------------------------+


*Parameter #8---input TXID*

+-----------+--------------+----------------------+-----------------------------------+
| Name      | Type         | Presence             | Description                       |
+-----------+--------------+----------------------+-----------------------------------+
| inputtxid | String (hex) | Required (exactly 1) | Domain asset input transaction id |
+-----------+--------------+----------------------+-----------------------------------+


*Parameter #9---input transaction vout*

+------+---------+----------------------+--------------------------------+
| Name | Type    | Presence             | Description                    |
+------+---------+----------------------+--------------------------------+
| vout | Integer | Required (exactly 1) | Domain asset input vout number |
+------+---------+----------------------+--------------------------------+


*Result---the unsigned raw transaction in hex, or JSON RPC error*

+--------+--------------+-----------------------------+
| Name   | Type         | Description                 |
+--------+--------------+-----------------------------+
| result | String (hex) | Hex encoded raw transaction |
+--------+--------------+-----------------------------+

*Example*

.. code-block:: bash

   ocean-cli createrawissuance 2deJ6F3w6HUtXM8JjY5YPc8wtaXerqFX7HA 123.0 2ddSmTujABoCzDyU9hPghcp4ojAGmJRtnWk 1.23 XKSxznoA799169xt3zCm7a4qkdT1KZANv3 332.9995 1 0.0005 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a 0

Result:

.. code-block:: text

   0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754d4c0040174820afc79a50ed4b9de7bfbc286adead7d46300787e3d986dd8bff570902d620100000002dd231b00001976a9143565dfe051b252c6c5d51275fa91850ef0bb26b288ac017c2096b20b81bd5734cb631335fa1a5bfed9c5a10e2ba4ce86df1b28fb2d50e401000000000754d4c0001976a9142c11d12a57cdd294b3ffa89cf2503a612252fbf288ac01230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b20100000007c0d4e9b00017a91458d6453537165062f887d004b979b054c5fa98c28701230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b201000000000000c350000000000000



createrawreissuance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawreissuance`` RPC creates a raw unsigned re-issuance (asset inflation) transaction with specified
outputs and spending from a specified input containing a valid re-issuance token.

*Parameter #1---the Base58check address for the re-issued asset*

+--------------+----------------------+----------------------+------------------------------------------------+
| Name         | Type                 | Presence             | Description                                    |
+--------------+----------------------+----------------------+------------------------------------------------+
| assetaddress | String (base58check) | Required (exactly 1) | Base58check address to send re-issued asset to |
+--------------+----------------------+----------------------+------------------------------------------------+


*Parameter #2---the amount of re-issued asset*

+-------------+--------+----------------------+-----------------------------+
| Name        | Type   | Presence             | Description                 |
+-------------+--------+----------------------+-----------------------------+
| assetamount | Amount | Required (exactly 1) | Amount of asset to re-issue |
+-------------+--------+----------------------+-----------------------------+


*Parameter #3---the Base58check address for the reissuance token*

+--------------+----------------------+----------------------+--------------------------------------+
| Name         | Type                 | Presence             | Description                          |
+--------------+----------------------+----------------------+--------------------------------------+
| tokenaddress | String (base58check) | Required (exactly 1) | Base58check reissuance token address |
+--------------+----------------------+----------------------+--------------------------------------+


*Parameter #4---the amount of reissuance token*

+-------------+--------+----------------------+----------------------------------------+
| Name        | Type   | Presence             | Description                            |
+-------------+--------+----------------------+----------------------------------------+
| tokenamount | Amount | Required (exactly 1) | Amount of reissuance token             |
+-------------+--------+----------------------+----------------------------------------+


*Parameter #5---input TXID*

+-----------+--------------+----------------------+----------------------------------------+
| Name      | Type         | Presence             | Description                            |
+-----------+--------------+----------------------+----------------------------------------+
| inputtxid | String (hex) | Required (exactly 1) | Re-issuance token input transaction ID |
+-----------+--------------+----------------------+----------------------------------------+


*Parameter #6---input transaction vout*

+------+---------+----------------------+-------------------------------------+
| Name | Type    | Presence             | Description                         |
+------+---------+----------------------+-------------------------------------+
| vout | Integer | Required (exactly 1) | Re-issuance token input vout number |
+------+---------+----------------------+-------------------------------------+


*Parameter #7---the asset entropy*

+---------+--------------+----------------------+----------------------------+
| Name    | Type         | Presence             | Description                |
+---------+--------------+----------------------+----------------------------+
| entropy | String (hex) | Required (exactly 1) | Hex encoded asset entropy  |
+---------+--------------+----------------------+----------------------------+


*Result---the unsigned raw transaction in hex, or JSN RPC error*

+--------+--------------+-----------------------------+
| Name   | Type         | Description                 |
+--------+--------------+-----------------------------+
| result | String (hex) | Hex encoded raw transaction |
+--------+--------------+-----------------------------+


*Example*

.. code-block:: bash

   ocean-cli createrawissuance 2deJ6F3w6HUtXM8JjY5YPc8wtaXerqFX7HA 0.5 2ddSmTujABoCzDyU9hPghcp4ojAGmJRtnWk 1.00 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a 0 b2e15d0d7a0c94e4e2ce0fe6e8691b9e451377f6e46e8045a86f7c4b5d4f0f23

Result:

.. code-block:: text

   0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754d4c0040174820afc79a50ed4b9de7bfbc286adead7d46300787e3d986dd8bff570902d620100000002dd231b00001976a9143565dfe051b252c6c5d51275fa91850ef0bb26b288ac017c2096b20b81bd5734cb631335fa1a5bfed9c5a10e2ba4ce86df1b28fb2d50e401000000000754d4c0001976a9142c11d12a57cdd294b3ffa89cf2503a612252fbf288ac01230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b20100000007c0d4e9b00017a91458d6453537165062f887d004b979b054c5fa98c28701230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b201000000000000c350000000000000



createrawburn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawburn`` RPC creates a raw unsigned burn transaction with a single input containing the burn amount and a single OP_RETURN output.

*Parameter #1---input TXID*

+------+--------------+----------------------+-------------------------------------------------------+
| Name | Type         | Presence             | Description                                           |
+------+--------------+----------------------+-------------------------------------------------------+
| txid | String (hex) | Required (exactly 1) | Input transaction ID containing the asset to be burnt |
+------+--------------+----------------------+-------------------------------------------------------+


*Parameter #2---input transaction vout*

+------+---------+----------------------+------------------------+
| Name | Type    | Presence             | Description            |
+------+---------+----------------------+------------------------+
| vout | Integer | Required (exactly 1) | Input transaction vout |
+------+---------+----------------------+------------------------+


*Parameter #3---the asset type*

+-------+--------------+----------------------+-------------------------------+
| Name  | Type         | Presence             | Description                   |
+-------+--------------+----------------------+-------------------------------+
| asset | String (hex) | Required (exactly 1) | Asset ID of asset to be burnt |
+-------+--------------+----------------------+-------------------------------+


*Parameter #4---the amount to burn (must equal the input amount)*

+--------+--------+----------------------+-------------------------+
| Name   | Type   | Presence             | Description             |
+--------+--------+----------------------+-------------------------+
| amount | Amount | Required (exactly 1) | Amount of asset to burn |
+--------+--------+----------------------+-------------------------+


*Result---the unsigned raw transaction in hex*

+--------+--------------+--------------------------------------+
| Name   | Type         | Description                          |
+--------+--------------+--------------------------------------+
| result | String (hex) | Hex encoded unsigned raw transaction |
+--------+--------------+--------------------------------------+

*Example*

.. code-block:: bash

   ocean-cli createrawissuance 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a 0 b2e15d0d7a0c94e4e2ce0fe6e8691b9e451377f6e46e8045a86f7c4b5d4f0f23 0.342100

Result:

.. code-block:: text

   0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754dee2e4940c7a0d5de1b201000000000000c350000000000000



testmempoolaccept
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``testmempoolaccept`` RPC determines the validity of a raw transaction without broadcasting it. It performs the exact same validity checks as performed on mempool acceptance, including locally configured policy rules, but without adding the transaction to the mempool.

*Parameter #1---signed raw transaction*

+--------+--------------+----------------------+---------------------------------------+
| Name   | Type         | Presence             | Description                           |
+--------+--------------+----------------------+---------------------------------------+
| rawtxs | String (hex) | Required (exactly 1) | Hex encodings of signed transacitons  |
+--------+--------------+----------------------+---------------------------------------+



*Parameter #2---accept large fee*

+---------------+---------+-------------------+-----------------+
| Name          | Type    | Presence          | Description     |
+---------------+---------+-------------------+-----------------+
| allowhighfees | Boolean | Optional (0 or 1) | Allow large fee |
+---------------+---------+-------------------+-----------------+


*Result---a JSON object containing the transaction ID, whether the transaction is accepted or rejected, and if rejected the reason*

+---------------+--------------+---------------------------------------------------------------+
| Name          | Type         | Description                                                   |
+---------------+--------------+---------------------------------------------------------------+
| txid          | String (hex) | The raw transaction ID                                        |
+---------------+--------------+---------------------------------------------------------------+
| allowed       | boolean      | The determination on whether the transaction is valid/allowed |
+---------------+--------------+---------------------------------------------------------------+
| reject-reason | String       | The reason if the transaction is rejected                     |
+---------------+--------------+---------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli testmempoolaccept 0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754dee2e4940c7a0d5de1b201000000000000c350000000000000

Result:

.. code-block:: json

   {
    "txid": 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a
    "accept": 1
   }



createrawpolicytx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawpolicytx`` RPC creates a raw unsigned policy transaction that encodes an address to be added to a policy list. To be accepted, the asset type must match the policy asset type as defined in the genesis block (via ``-freezelistcoinsdestination`` and ``-burnlistcoinsdestination``). The policy asset input(s) are specified in an array, and the outputs are specified in an array of objects that contain a policy public key, the address to be added to to the policy list and the value. Spending these outputs removes the addresses from the policy lists.

*Parameter #1---Inputs*

+------------+--------------+----------------------+-------------------------------------------------------------------------+
| Name       | Type         | Presence             | Description                                                             |
+------------+--------------+----------------------+-------------------------------------------------------------------------+
| inputs     | array        | Required (exactly 1) | An array of objects, each one to be used as an input to the transaction |
+------------+--------------+----------------------+-------------------------------------------------------------------------+
|            | Object       | Required(1 or more)  | An object describing a particular input with fields listed below        |
| →          |              |                      |                                                                         |
| `Input`    |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------------------------+
| →→         | String (hex) | Required(exactly 1)  | The transaction ID of the output to be spent in hex encoding            |
| `txid`     |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------------------------+
| →→         | Integer      | Required(exactly 1)  | The output index number (vout) of the output to be spent                |
| `vout`     |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------------------------+
| →→         | Integer      | Optional (0 or 1)    | The sequence number to use for the input                                |
| `sequence` |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------------------------+


*Parameter #2---Addresses to add to policy lists and control keys*

+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| Name          | Type                 | Presence             | Description                                                              |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| policy output | array                | Required (exactly 1) | An array of objects, each one is used to add an address to a policy list |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| →             | Object               | Required (1 or more) | An object encoding a policy output, with fields listed below             |
| `Input`       |                      |                      |                                                                          |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| →→            | String (hex)         | Required (exactly 1) | The public key of the policy authority wallet used to spend the output   |
| `pubkey`      |                      |                      |                                                                          |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| →→            | Amount               | Required (exactly 1) | The amount of policy asset to be sent to the output                      |
| `value`       |                      |                      |                                                                          |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+
| →→            | String (base58check) | Required (exactly 1) | The address to be added to the policy list                               |
| `address`     |                      |                      |                                                                          |
+---------------+----------------------+----------------------+--------------------------------------------------------------------------+


*Parameter #3---locktime*

+----------+---------+---------------------+----------------------------------------------------------------------------------------------------------------+
| Name     | Type    | Presence            | Description                                                                                                    |
+----------+---------+---------------------+----------------------------------------------------------------------------------------------------------------+
| locktime | Integer | Required(exactly 1) | Indicates the earliest time (in block height or Unix epoch time) a transaction can be added to the block chain |
+----------+---------+---------------------+----------------------------------------------------------------------------------------------------------------+


*Parameter #4---policy asset*

+--------------+--------------+----------------------+--------------------------------------------------------------------+
| Name         | Type         | Presence             | Description                                                        |
+--------------+--------------+----------------------+--------------------------------------------------------------------+
| policy asset | String (hex) | Required (exactly 1) | The 32 byte asset ID of the policy asset of the list being updated |
+--------------+--------------+----------------------+--------------------------------------------------------------------+


*Result---the unsigned raw transaction in hex. If the transaction couldn't be generated, result will be set to JSON `null` and the JSON-RPC error field may contain an error message*

+--------+--------------+-------------------------------------------------------------------------+
| Name   | Type         | Description                                                             |
+--------+--------------+-------------------------------------------------------------------------+
| result | String (hex) | The resulting unsigned raw transaction in serialized transaction format |
+--------+--------------+-------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli createrawpolicytx '''[
     {
       "txid": "43bd75af773cce38fd190f6c0943d311ce2dd8a26c7e7a9e600c58f8b21e53d4",
       "vout": 1
     }
   ]''' '''[
     {
       "pubkey": "03d5be1ca0b06b54f6a29a8e245fdf58698164538191c5b376d3b27e6d3229b81a",
       "value": 10.0
       "address": "1HPkc4to3GzVcEV8Le6sS4V5AXWQceH5kZ"
     }
   ]'''

Result:

.. code-block:: text

   0200000000018eb8f2e93d81b9f904f8613b6520460c00f9313683b5574fc8b67c3e33e615550000000000ffffffff010131e34d9c314f12348e8b2cb401d07450ef5e831d11bba35bd9b124f52e78387301000000746a5288000047512103e6ec6419791bcdc9ec5b2cf7d25cfffa244f2cddfd9997dffbd002c4d88baabe21020000000000000000000000005923c34a75800d2e9a6894d846649b65995dc84252ae00000000



createrawrequesttx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawrequesttx`` RPC creates a raw request transaction with a single input and single output.

*Parameter #1---Input object with details on transaction to be spent*

+------------+--------------+----------------------+-----------------------------------------------------+
| Name       | Type         | Presence             | Description                                         |                  
+------------+--------------+----------------------+-----------------------------------------------------+
| →          | Object       | Required(1 or more)  | An object describing an input transaction           |
| `Input`    |              |                      |                                                     |                    
+------------+--------------+----------------------+-----------------------------------------------------+
| →→         | String (hex) | Required(exactly 1)  |  The txid of the input transaction                  |
| `txid`     |              |                      |                                                     |
+------------+--------------+----------------------+-----------------------------------------------------+
| →→         | Integer      | Required(exactly 1)  | The output index number (vout) of input transaction |
| `vout`     |              |                      |                                                     |
+------------+--------------+----------------------+-----------------------------------------------------+


*Parameter #2---Output object with request details*

+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| Name               | Type         | Presence             | Description                                                       |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| Output             | Object       | Required(1 or more)  | An object identifying an output                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | string (hex) | Required (exactly 1) | Target request public key                                         |
| `pubkey`           |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
|                    | Number       | Required (exactly 1) | Decay constant determining speed at which tickets value decreases |
| →                  |              |                      |                                                                   |
| `decayConst        |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Integer      | Required (exactly 1) | End block request servcie period                                  |
| `endBlockHeight``  |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Amount       | Required (exactly 1) | Percentage of fees to be paid to guardnodes                       |
| `fee`              |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | String       | Required (exactly 1) | Hash of client chain genesis block                                |
| `genesisBlockHash` |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Integer      | Required (exactly 1) | Start block request servcie period, end block of auction period   |
| `startBlockHeight` |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Amount       | Required (exactly 1) | Starting price of ticket                                          |
| `startPrice`       |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Integer      | Required (exactly 1) | Number of tickets to issue                                        |
| `tickets`          |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+
| →                  | Amount       | Required (exactly 1) | value of input transaction                                        |
| `value`            |              |                      |                                                                   |
+--------------------+--------------+----------------------+-------------------------------------------------------------------+


*Result---the unsigned raw transaction in hex. If the transaction couldn't be generated, result will be set to JSON `null` and the JSON-RPC error field may contain an error message*

+--------+--------------+-------------------------------------------------------------------------+
| Name   | Type         | Description                                                             |
+--------+--------------+-------------------------------------------------------------------------+
| result | String (hex) | The resulting unsigned raw transaction in serialized transaction format |
+--------+--------------+-------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli createrawrequesttx '''[
     {
       "txid": "43bd75af773cce38fd190f6c0943d311ce2dd8a26c7e7a9e600c58f8b21e53d4",
       "vout": 1,
     }
   ]''' '''[
     {
       "pubkey": "03d5be1ca0b06b54f6a29a8e245fdf58698164538191c5b376d3b27e6d3229b81a",
       "decayConst": 5,
       "endBlockHeight": 250,
       "fee": 5,
       "genesisBlockHash": "99bd75af773cce38fd190f6c0943d311ce2dd8a26c7e7a9e600c58f8b21e53d4",
       "startBlockHeight": 100,
       "startPrice": 5.0,
       "tickets": 150,
       "value": 1000.0
     }
   ]'''


Result:

.. code-block:: text

   02000000000151227925212487ef62c10e46f14aec78dce956b02eb41f7e2cce8b6d56292db40100000000feffffff0101d08413554d89a69f0d93a6f7e33242d472a6503b11b1b7c10d3134afb2a36d0101000000174876e800006d0169b17551210246e99744bdee2ce153eb6185019e73ff6bb4d050d50d1a1d7d773f45474d4c362102051d7e7caa636a8fb1eaca4fb163248aff57d5e4e04e84734101b138e1a07d862103640000000a0000000a000000010000000000000000000000000000000000000053ae66000000



getrequests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getrequests`` RPC returns all the active client requests in the blockchain.

*Parameter #1---the client genesis block hash*

+-------------+---------+----------+----------------------------------------------+
| Name        | Type    | Presence | Description                                  |
+-------------+---------+----------+----------------------------------------------+
| genesishash | string  | Optional | The genesis block hash to filter requests by |
+-------------+---------+----------+----------------------------------------------+


*Result---an array of JSON objects containing details for each active request*

+------------------+--------------+-------------------------------------------------------------+
| Name             | Type         | Description                                                 |
+------------------+--------------+-------------------------------------------------------------+
| result           | array        | array of JSON objects of details for each active request    |
+------------------+--------------+-------------------------------------------------------------+
| →                | string (hex) | JSON objects containing details for each active request     |
| `request`        |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | string       | Client chain genesis block hash                             |
| `genesisBlock`   |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Integer      | Number of tickets available                                 |
| `numTickets`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Number       | Decay constant determining speed at which tickets value     |
| `decayConst`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Starting price of ticket                                    |
| `startPrice`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Current price of tickets                                    |
| `auctionPrice`   |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Percentage of fees to be paid to guardnodes                 |
| `feePercentage`  |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Integer      | End block request servcie period                            |
| `endBlockHeight` |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | String (hex) | Request transaction ID                                      |
| `txid`           |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli getrequests
   ocean-cli getrequests 123450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05

Result:

.. code-block:: json

   [
     {
       "genesisBlock": "123450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05",
       "startBlockHeight": 105,
       "numTickets": 20,
       "decayConst": 2,
       "startPrice": 5.0,
       "auctionPrice": 4.8,
       "feePercentage": 5,
       "endBlockHeight": 350,
       "txid": "666450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05"
     },
   ]



createrawbidtx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawbidtx`` RPC creates a raw bid transaction funded by given inputs. Bids must be in the domain asset.

*Parameter #1---Input object with details on transactions to be spent*

+------------+--------------+----------------------+------------------------------------------------------+
| Name       | Type         | Presence             | Description                                          |                  
+------------+--------------+----------------------+------------------------------------------------------+
|            | Object       | Required(1 or more)  | An object describing an input transaction            |
| →          |              |                      |                                                                         
| `Input`    |              |                      |                                                                         
+------------+--------------+----------------------+-------------------------------------------------------+
| →→         | String (hex) | Required(exactly 1)  |  The txid of the input transaction                    |
| `txid`     |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------+
| →→         | Integer      | Required(exactly 1)  | T The output index number (vout) of input transaction |
| `vout`     |              |                      |                                                                         |
+------------+--------------+----------------------+-------------------------------------------------------+


*Parameter #2---Output object with request details*

+-----------------+----------------------+----------------------+---------------------------------------------+
| Name            | Type                 | Presence             | Description                                 |
+-----------------+----------------------+----------------------+---------------------------------------------+
| Output          | object               | Required (1 or more) | JSON objects identifying an output          |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | string (hex)         | Required (exactly 1) | Target stake public key                     |
| `pubkey`        |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | Amount               | Required (exactly 1) | Staked value locked in target pubkey        |
| `value`         |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | Amount               | Optional (0 or 1)    | Change value of transaction                 |
| `change`        |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | String (base58check) | Optional (0 or 1)    | Change address of transaction               |
| `changeAddr`    |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | Amount               | Required (exactly 1) | Fee value of transaction                    |
| `fee`           |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | Integer              | Required (exactly 1) | Service end block height                    |
| `endBlockHeigt` |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | String (hex)         | Required (exactly 1) | Percentage of fees to be paid to guardnodes |
| `requestTxid`   |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+
| →               | String (hex)         | Required (exactly 1) | Public key to receive fees on client chain  |
| `feePubKey`     |                      |                      |                                             |
+-----------------+----------------------+----------------------+---------------------------------------------+


*Result---the unsigned raw transaction in hex. If the transaction couldn't be generated, result will be set to JSON `null` and the JSON-RPC error field may contain an error message*

+--------+--------------+-------------------------------------------------------------------------+
| Name   | Type         | Description                                                             |
+--------+--------------+-------------------------------------------------------------------------+
| result | String (hex) | The resulting unsigned raw transaction in serialized transaction format |
+--------+--------------+-------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli createrawbidtx '''[
     {
       "txid": "43bd75af773cce38fd190f6c0943d311ce2dd8a26c7e7a9e600c58f8b21e53d4",
       "vout": 1,
     }
   ]''' '''[
     {
       "pubkey": "03d5be1ca0b06b54f6a29a8e245fdf58698164538191c5b376d3b27e6d3229b81a",
       "value": 50,
       "change": 15,
       "changeAddr": "2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz",
       "fee": 5,
       "endBlockHeight": 250,
       "requestTxid": "666450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05",
       "feePubKey": "05bf356342a0aa4f6a29a8e245fdf58698164538191c5b376d3b27ebc7816e1a0"
     }
   ]'''

Result:

.. code-block:: text

   02000000000564739500498487ef62c10e46f14aec78dce956b02eb41f7e2cce8b6d56292db40100000000feffffff0101d08413554d89a69f0d93a6f7e33242d472a6503b11b1b7c10d3134afb2a36d0101000000174876e800006d0169b17551210246e99744bdee2ce153eb6185019e73ff6bb4d050d50d1a1d7d773f45474d4c362102051d7e7caa636a8fb1eaca4fb163248aff57d5e4e04e84734101b138e1a07d862103640000000a0000000a000000010000000000000000000000000000000000000053ae66000000



getrequestbids
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getrequestbids`` RPC returns all the active bids for a given requests in the blockchain.

*Parameter #1---the request transaction hash*

+-------------+---------+----------+----------------------------------------------------+
| Name        | Type    | Presence | Description                                        |
+-------------+---------+----------+----------------------------------------------------+
| requesthash | string  | Optional | Hash of the request tranasction to return bids for |
+-------------+---------+----------+----------------------------------------------------+


*Result---an array of JSON objects containing details for each active request and its corresponding bids*

+------------------+--------------+-------------------------------------------------------------+
| Name             | Type         | Description                                                 |
+------------------+--------------+-------------------------------------------------------------+
| result           | array        | array of JSON objects of details for each active request    |
+------------------+--------------+-------------------------------------------------------------+
| →                | string (hex) | JSON objects containing details for each active request     |
| `request`        |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | string       | Client chain genesis block hash                             |
| `genesisBlock`   |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Integer      | Number of tickets available                                 |
| `numTickets`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Number       | Decay constant determining speed at which tickets value     |
| `decayConst`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Starting price of ticket                                    |
| `startPrice`     |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Current price of tickets                                    |
| `auctionPrice`   |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Amount       | Percentage of fees to be paid to guardnodes                 |
| `feePercentage`  |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | Integer      | End block request servcie period                            |
| `endBlockHeight` |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | String (hex) | Request transaction ID                                      |
| `txid`           |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→               | array        | array of objects containing bids information                |
| `bids`           |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→→              | String       | hash of bid transaction                                     |
| `hash`           |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+
| →→→              | String (hex) | Public key that receives fees on client service chain       |
| `feePubKey`      |              |                                                             |
+------------------+--------------+-------------------------------------------------------------+

*Example*

.. code-block:: bash

   ocean-cli getrequestbids
   ocean-cli getrequestbids 123450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05

Result:

.. code-block:: json

   [
     {
       "genesisBlock": "123450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05",
       "startBlockHeight": 105,
       "numTickets": 20,
       "decayConst": 2,
       "startPrice": 5.0,
       "auctionPrice": 4.8,
       "feePercentage": 5,
       "endBlockHeight": 350,
       "txid": "666450e138b1014173844ee0e4d557ff8a2463b14fcaeab18f6a63aa7c7e1d05"
       "bids": [
         {
            "hash":2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz,
            "feePubKey":05bf356342a0aa4f6a29a8e245fdf58698164538191c5b376d3b27ebc7816e1a0,
         }
       ]
     },
   ]



addtowhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addtowhitelist`` RPC adds a valid contract tweaked address to the node
mempool whitelist. It requires both an address and corresponding base public
key, and the RPC checks that the address is valid and has been tweaked
from the supplied base public key with the current
contract hash as present in the most recent block header.

*Parameter #1---the Base58check contract tweaked address*

+---------+----------------------+----------------------+----------------------------------------------+
| Name    | Type                 | Presence             | Description                                  |
+---------+----------------------+----------------------+----------------------------------------------+
| address | String (base58check) | Required (exactly 1) | Base58check encoded contract tweaked address |
+---------+----------------------+----------------------+----------------------------------------------+


*Parameter #2---the base (un-tweaked) compressed public key*

+---------+--------------+----------------------+-----------------------------+
| Name    | Type         | Presence             | Description                 |
+---------+--------------+----------------------+-----------------------------+
| bpubkey | String (hex) | Required (exactly 1) | Hex encoded base public key |
+---------+--------------+----------------------+-----------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addtowhitelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz 028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58



addmultitowhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addmultitowhitelist`` RPC adds a valid contract tweaked p2sh (multisig) address to the node
mempool whitelist. It requires an address, number of required signatures and corresponding base public
keys, and the RPC checks that the address is valid and has been tweaked
from the supplied base public keys with the current
contract hash as present in the most recent block header.

*Parameter #1---the Base58check contract tweaked p2sh address*

+----------------+----------------------+----------------------+---------------------------------------------------+
| Name           | Type                 | Presence             | Description                                       |
+----------------+----------------------+----------------------+---------------------------------------------------+
| tweakedaddress | String (base58check) | Required (exactly 1) | Base58check encoded contract tweaked p2sh address |
+----------------+----------------------+----------------------+---------------------------------------------------+


*Parameter #2---the base (un-tweaked) compressed public keys that the p2sh was created with*

+-------------+-------+----------------------+------------------------------+
| Name        | Type  | Presence             | Description                  |
+-------------+-------+----------------------+------------------------------+
| basepubkeys | array | Required (1 or more) | Hex encoded base public keys |
+-------------+-------+----------------------+------------------------------+


*Parameter #3---the n of Multisig*

+-------------+---------+----------------------+--------------------------------------------+
| Name        | Type    | Presence             | Description                                |
+-------------+---------+----------------------+--------------------------------------------+
| nmultisig   | Integer | Required (exactly 1) | Number of signatures required for multisig |
+-------------+---------+----------------------+--------------------------------------------+


*Parameter #4---the Base58 KYC address*

+-------------+-----------------------+----------------------+--------------------------------------------+
| Name        | Type                  | Presence             | Description                                |
+-------------+-----------------------+----------------------+--------------------------------------------+
| kycaddress   | string (base58check) | Optional (0 or 1)    | Base58check encoded KYC address            |
+-------------+-----------------------+----------------------+--------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addmultitowhitelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz [028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58,028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58] 1



querywhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``querywhitelist`` RPC queries if a specified address is present in the node mempool whitelist.

*Parameter #1---the Base58check encoded address*

+-------------+-----------------------+----------------------+--------------------------------------------+
| Name        | Type                  | Presence             | Description                                |
+-------------+-----------------------+----------------------+--------------------------------------------+
| address     | string (base58check)  | Required (exactly 1) | Base58check encoded address                |
+-------------+-----------------------+----------------------+--------------------------------------------+


*Result---TRUE of FALSE*

+--------+---------+------------------------------------------+
| Name   | Type    | Description                              |
+--------+---------+------------------------------------------+
| result | boolean | 1 if the address is present, 0 otherwise |
+--------+---------+------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli querywhitelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz

Result:

.. code-block:: text

   1



readwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``readwhitelist`` RPC adds a list of valid contract tweaked address to the node
mempool whitelist. It requires a file that contains a list of both an address and corresponding base public
key, and the RPC cheacks that each address is valid and has been tweaked
from the supplied base public key with the current
contract hash as present in the most recent block header. The file format is as decribed in `dumpderivedkeys <#dumpderivedkeys>`_.

*Parameter #1---the filename to read in the list*

+----------+---------+----------------------+-----------------------------------------------------------------------------------+
| Name     | Type    | Presence             | Description                                                                       |
+----------+---------+----------------------+-----------------------------------------------------------------------------------+
| filename | string  | required (exactly 1) | Name of file containing the list of contract tweaked address and base public keys |
+----------+---------+----------------------+-----------------------------------------------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli readwhitelist derivedkeys.txt



removefromwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``removefromwhitelist`` RPC removes a specified address from the node mempool whitelist.

*Parameter #1---the Base58check encoded address*

+-------------+-----------------------+----------------------+--------------------------------------------+
| Name        | Type                  | Presence             | Description                                |
+-------------+-----------------------+----------------------+--------------------------------------------+
| address     | string (base58check)  | Required (exactly 1) | Base58check encoded address                |
+-------------+-----------------------+----------------------+--------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli removefromwhitelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz



clearwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``clearwhitelist`` RPC clears the mempool whitelist of all addresses.

*Parameters: none*

*Result: none*

*Example*

.. code-block:: bash

   ocean-cli clearwhitelist



dumpwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dumpwhitelist`` RPC outputs a list of all
addresses in the node mempool whitelist to a specified file.

*Parameter #1---the filename of the output file*

+----------+--------+----------------------+------------------------------------------------------------------------------------+
| Name     | Type   | Presence             | Description                                                                        |
+----------+--------+----------------------+------------------------------------------------------------------------------------+
| filename | String | Required (exactly 1) |  The name of the output file for the list of whitelist addresses                     |
+----------+--------+----------------------+------------------------------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli dumpwhitelist dumpfile.txt



sendaddmultitowhitelisttx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendaddmultitowhitelisttx`` RPC serializes and sends an OP_REGISTERADDRESS transaction for multisig which is used to add a valid contract tweaked p2sh (multisig) address to the whitelist. It requires an address, number of required signatures and corresponding base public
keys, and the RPC checks that the address is valid and has been tweaked
from the supplied base public keys with the current
contract hash as present in the most recent block header. Whitelist node reads the transaction and adds the address to a whitelist if details are valid.

*Parameter #1---the Base58check contract tweaked p2sh address*

+----------------+----------------------+----------+---------------------------------------------------+
| Name           | Type                 | Presence | Description                                       |
+----------------+----------------------+----------+---------------------------------------------------+
| tweakedaddress | string (base58check) | Required | Base58check encoded contract tweaked p2sh address |
+----------------+----------------------+----------+---------------------------------------------------+


*Parameter #2---the base (un-tweaked) compressed public keys that the p2sh was created with*

+-------------+-------+----------------------+------------------------------+
| Name        | Type  | Presence             | Description                  |
+-------------+-------+----------------------+------------------------------+
| basepubkeys | array | Required (1 or more) | Hex encoded base public keys |
+-------------+-------+----------------------+------------------------------+


*Parameter #3---the n of Multisig*

+-------------+---------+----------------------+--------------------------------------------+
| Name        | Type    | Presence             | Description                                |
+-------------+---------+----------------------+--------------------------------------------+
| nmultisig   | Integer | Required (exactly 1) | Number of signatures required for multisig |
+-------------+---------+----------------------+--------------------------------------------+

*Parameter #4---the fee asset type*

+----------+---------+-------------------+-------------------------------+
| Name     | Type    | Presence          | Description                   |
+----------+---------+-------------------+-------------------------------+
| feeasset | String  | Optional (0 or 1) | Type of asset for fee payment |
+----------+---------+-------------------+-------------------------------+


*Result---transaction hex if valid, errors returned if invalid inputs or wallet error occurs*

+--------+--------------+------------------------------------------------------------+
| Name   | Type         | Description                                                |
+--------+--------------+------------------------------------------------------------+
| result | String (hex) | The resulting transaction id serialized transaction format |
+--------+--------------+------------------------------------------------------------+

*Example*

.. code-block:: bash

   ocean-cli sendaddmultitowhitelisttx 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz [028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58,028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58] 1



sendaddtowhitelisttx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendaddtowhitelisttx`` RPC serializes and sends an OP_REGISTERADDRESS transaction which is used to add valid contract tweaked addresses to the whitelist (they are automatically retrieved from the wallet pool). Whitelist node reads the transaction and adds the addresses to a whitelist if details are valid.

*Parameter #1---Number of addresses that should be taken from the wallet pool and whitelisted*

+------------+----------+----------------------+---------------------------------+
| Name       | Type     | Presence             | Description                     |
+------------+----------+----------------------+---------------------------------+
| naddresses | Integer  | Required (exactly 1) | Number of addresses to register |
+------------+----------+----------------------+---------------------------------+


*Parameter #2---the fee asset type*

+----------+--------+--------------------+-----------------------+
| Name     | Type   | Presence           | Description           |
+----------+--------+--------------------+-----------------------+
| feeasset | String | Optional (0 or 1)  | Type of asset for fee |
+----------+--------+--------------------+-----------------------+


*Result---transaction hex if valid, errors returned if invalid inputs or wallet error*

+--------+--------------+------------------------------------------------------------+
| Name   | Type         | Description                                                |
+--------+--------------+------------------------------------------------------------+
| result | String (hex) | The resulting transaction id serialized transaction format |
+--------+--------------+------------------------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli sendaddtowhitelisttx 100



addtofreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addtofreezelist`` RPC adds an address to the node
mempool freezelist. Transactions spending from UTXOs with
output addresses on the freezelist are blocked from entering the
mempool if the '-freezelist' configuration option is enabled.

*Parameter #1---the Base58check address*

+---------+----------------------+----------------------+-------------------------------------------+
| Name    | Type                 | Presence             | Description                               |
+---------+----------------------+----------------------+-------------------------------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address to add to freeze list |
+---------+----------------------+----------------------+-------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addtofreezelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz



queryfreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``queryfreezelist`` RPC queries if a specified address is present in the node mempool freezelist.

*Parameter #1---the Base58check encoded address*

+---------+----------------------+----------------------+---------------------+
| Name    | Type                 | Presence             | Description         |
+---------+----------------------+----------------------+---------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address |
+---------+----------------------+----------------------+---------------------+


*Result---TRUE of FALSE*

+--------+---------+------------------------------------------+
| Name   | Type    | Description                              |
+--------+---------+------------------------------------------+
| result | Boolean | 1 if the address is present, 0 otherwise |
+--------+---------+------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli queryfreezelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz

Result:

.. code-block:: text

   1



removefromfreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``removefromfreezelist`` RPC removes a specified address from the node mempool freezelist.

*Parameter #1---the Base58check encoded address*

+---------+----------------------+----------------------+------------------------------------------------+
| Name    | Type                 | Presence             | Description                                    |
+---------+----------------------+----------------------+------------------------------------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address to remove from freeze list |
+---------+----------------------+----------------------+------------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli removefromfreezelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz



clearfreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``clearfreezelist`` RPC clears the mempool whitelist of all addresses.

*Parameters: none*

*Result: none*

*Example*

.. code-block:: bash

   ocean-cli clearfreezelist



addtoburnlist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addtoburnlist`` RPC adds an address to the node
mempool burnlist. Transactions spending from UTXOs with
output addresses on the freezelist are alowed into the
mempool if these addresses are also on the burnlist and have
only TX_FEE and TX_NULL_DATA outputs (with both the ``-freezelist``
and ``-burnlist`` configurations options enabled).

*Parameter #1---the Base58check address*

+---------+----------------------+----------------------+-------------------------------------------+
| Name    | Type                 | Presence             | Description                               |
+---------+----------------------+----------------------+-------------------------------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address to add to freeze list |
+---------+----------------------+----------------------+-------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addtoburnlist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz



queryburnlist
^^^^^^^^^^^^^^

The ``queryburnlist`` RPC queries if a specified address is present in the node mempool burnlist.

*Parameter #1---the Base58check encoded address*

+---------+----------------------+----------------------+---------------------+
| Name    | Type                 | Presence             | Description         |
+---------+----------------------+----------------------+---------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address |
+---------+----------------------+----------------------+---------------------+


*Result---TRUE of FALSE*

+--------+---------+------------------------------------------+
| Name   | Type    | Description                              |
+--------+---------+------------------------------------------+
| result | Boolean | 1 is the address is present, 0 otherwise |
+--------+---------+------------------------------------------+


*Example*

.. code-block:: bash

   ocean-cli queryburnlist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz

Result:

.. code-block:: text

   1



removefromburnlist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``removefromburnlist`` RPC removes a specified address from the node mempool burnlist.

*Parameter #1---the Base58check encoded address*

+---------+----------------------+----------------------+----------------------------------------------+
| Name    | Type                 | Presence             | Description                                  |
+---------+----------------------+----------------------+----------------------------------------------+
| address | String (base58check) | Required (exactly 1) | Base58check address to remove from burn list |
+---------+----------------------+----------------------+----------------------------------------------+


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli removefromburnlist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz



clearburnlist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``clearburnlist`` RPC clears the mempool whitelist of all addresses.

*Parameters: none*

*Result: none*

*Example*

.. code-block:: bash

   ocean-cli clearburnlist



pkhwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables node mempool address whitelisting. With this option set all addresses are blacklisted by default and must be added to the whitelist in order to transact.


*Argument---TRUE or FALSE*

+---------+---------+--------------------------+
| Type    | Default | Description              |
+---------+---------+--------------------------+
| Boolean | 0       | 1 to enable whitelisting |
+---------+---------+--------------------------+

*Example---The following examples enable whitelisting*

.. code-block:: bash

   oceand -pkhwhitelist

In ocean.conf:
   pkhwhitelist=1



freezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables node mempool address freezelisting.

*Argument---TRUE or FALSE*

+---------+---------+-------------------------+
| Type    | Default | Description             |
+---------+---------+-------------------------+
| Boolean | 0       | 1 to enable freeze list |
+---------+---------+-------------------------+



burnlist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables node mempool address burnlisting.

*Argument---TRUE or FALSE*

+---------+---------+-----------------------+
| Type    | Default | Description           |
+---------+---------+-----------------------+
| Boolean | 0       | 1 to enable burn list |
+---------+---------+-----------------------+



issuanceblock
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables blocking of invalid issuance transactions from mempool by checking that asset issuance transactions have an issuanceAsset input.

*Argument---TRUE or FALSE*

+---------+---------+-------------+
| Type    | Default | Description |
+---------+---------+-------------+
| Boolean | 0       | 1 to enable |
+---------+---------+-------------+



disablect
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Disables confidential transactions and addresses.

*Argument---TRUE or FALSE*

+---------+---------+----------------------------------------+
| Type    | Default | Description                            |
+---------+---------+----------------------------------------+
| Boolean | 0       | 1 to disable confidential transactions |
+---------+---------+----------------------------------------+



embedcontract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enable hash of chain's contract to be embedded in block header and addresses.

*Argument---TRUE or FALSE*

+---------+---------+-------------+
| Type    | Default | Description |
+---------+---------+-------------+
| Boolean | 0       | 1 to enable |
+---------+---------+-------------+

*Example---The following examples will disable contract hash embedding*

.. code-block:: bash

   oceand -embedcontract=0

In ocean.conf:
   embedcontract=0



attestationhash
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Embed the given attestation hash in the block header.

*Argument---Attestation hash*

+--------+---------+------------------------------------------+
| Type   | Default | Description                              |
+--------+---------+------------------------------------------+
| String | null    | 256-bit hex encoding of attestation hash |
+--------+---------+------------------------------------------+


*Example---The following will set the contract hash for embedding*

.. code-block:: bash

   oceand -attestationhash=aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15

In ocean.conf:
   attestationhash=aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15



embedmapping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enable asset mapping object embedding in block header.

*Argument---TRUE or FALSE*

+---------+-----------------------+
| Type    | Default | Description |
+---------+-----------------------+
| Boolean | 0       | 1 to enable |
+---------------------------------+



issuecontrolscript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Embed the given issuance controller script in the genesis block as a coinbase transaction in an op_return script. Does nothing if set after chain initialisation.

*Argument---issuance control script*

+--------------+---------+-----------------------------------------+
| Type         | Default | Description                             |
+--------------+---------+-----------------------------------------+
| String (hex) | null    | hex encoding of issuance control script |
+--------------+---------+-----------------------------------------+


policycoins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The amount of policy coins created in the genesis block.

*Argument---Number of policy coins to create*

+--------+---------+----------------------------------+
| Type   | Default | Description                      |
+--------+---------+----------------------------------+
| Amount | 0       | Amount of policy coins to create |
+--------+---------+----------------------------------+


initialfreecoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The destination of the OP_TRUE initial freecoins created in the genesis block. This functionality is used primarily for testing.

*Argument---Address to hold created coins*

+---------+---------+----------------------------------+
| Type    | Default | Description                      |
+---------+---------+----------------------------------+
| address | null    | Destination of created freecoins |
+---------+---------+----------------------------------+

*Example*

.. code-block:: bash

   oceand -initialfreecoinsdestination=76a91427e17844a9dff73ace482eae458105005ad672e488ac

In ocean.conf:
   initialfreecoinsdestination=76a91427e17844a9dff73ace482eae458105005ad672e488ac



freezelistcoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The destination of the tokens for controlling the freezelist.

*Argument---Address to hold created freeze tokens*

+---------+---------+------------------------------------------+
| Type    | Default | Description                              |
+---------+---------+------------------------------------------+
| address | null    | Destination of freeze list policy tokens |
+---------+---------+------------------------------------------+



burnlistcoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The destination of the tokens for controlling the burnlist.

*Argument---Address to hold created burn tokens*

+---------+---------+----------------------------------------+
| Type    | Default | Description                            |
+---------+---------+----------------------------------------+
| address | null    | Destination of burn list policy tokens |
+---------+---------+----------------------------------------+



issuancecoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The destination of the tokens for controlling issuances.

*Argument---Address to hold created issuance coins*

+---------+---------+---------------------------------------+
| Type    | Default | Description                           |
+---------+---------+---------------------------------------+
| address | null    | Destination of issuance policy tokens |
+---------+---------+---------------------------------------+



permissioncoinsdestination
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The destination of the policy tokens for permitting request creation.

*Argument---Address to hold created permitting request tokens*

+---------+---------+-----------------------------------------+
| Type    | Default | Description                             |
+---------+---------+-----------------------------------------+
| address | null    | Destination of permission policy tokens |
+---------+---------+-----------------------------------------+



mainchainrpchost
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The rpc host address which the daemon will try to connect to validate peg-ins, if enabled. By default cookie authentication is attempted.

*Argument---main chain rpc host*

+------+--------+---------------+
| Name | Type   | Description   |
+------+--------+---------------+
| host | String | host address  |
+------+--------+---------------+


mainchainrpcport
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The rpc port number which the daemon will try to connect to validate peg-ins, if enabled. By default cookie authentication is attempted.

*Argument---main chain rpc port*

+------+---------+-------------+
| Name | Type    | Description |
+------+---------+-------------+
| port | Integer | port number |
+------+---------+-------------+



validatepegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enable validation of all peg-in claims.



*Argument---TRUE or FALSE*

+---------+---------------------------------------------+
| Type    | Default | Description                       |
+---------+---------------------------------------------+
| Boolean | 0       | 1 to enable validation of peg-ins |
+-------------------------------------------------------+



parentgenesisblockhash
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set parent genesis blockhash. Ethereum mainnet is default.

*Argument---parent block hash*

+--------------+------------------------------------------------------------------+------------------------------+
| Type         | Default                                                          | Description                  |
+--------------+------------------------------------------------------------------+------------------------------+
| String (hex) | d4e56740f876aef8c010b86a40d5f56745a118d0906a34e69aec8c0db1cb8fa3 | Hash of desired parent block |
+--------------+------------------------------------------------------------------+------------------------------+


parentcontract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set parent ERC20 contract script. Enabling causes creation of a new chain with a different genesis block.

*Argument---ERC20 contract script*

+--------------+------------------------------------------+-------------------------------+
| Type         | Default                                  | Description                   |
+--------------+------------------------------------------+-------------------------------+
| String (hex) | 076C97e1c869072eE22f8c91978C99B4bcB02591 | Hex encodeing of ERC20 script |
+--------------+------------------------------------------+-------------------------------+


fedpegaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set ETH address of federated peg. Here a new chain is created with a different genesis block.

*Argument---Ethereum address*

+---------+---------+-----------------------------------+
| Type    | Default | Description                       |
+---------+---------+-----------------------------------+
| address | null    | Ethereum address of federated peg |
+---------+---------+-----------------------------------+


peginconfirmationdepth
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set required depth of network for peg-in claims to be considered valid.

*Argument---block height*

+--------------+---------+---------+--------------+
| Name         | Type    | Default | Description  |
+--------------+---------+---------+--------------+
| block height | Integer | 8       | block height |
+--------------+---------+---------+--------------+

