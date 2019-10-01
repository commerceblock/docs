Ocean API reference
===================

The Ocean client includes all of the Remote Procedure Calls (RPCs) of
the Elements platform (described in the reference
`here <https://github.com/ElementsProject/elementsbp-api-reference/blob/master/api.md>`_\ )
as well as additional RPCs that control advanced and extended features unique to the Ocean client.
This document describes these new RPCs and their function as well as additional
Ocean client configuration options that enable them.

For any RPC supported in the Ocean client (including those inherited from Elements and
Bitcoin), you can get information about function and correct usage from the command line
using the ``help`` RPC.  For example,

.. code-block:: text

   ocean-cli help getblockchaininfo
   
 
As in Elements and Bitcoin, the Configuration options can be passed to oceand as command line arguments or added to ocean.conf configuration file. For a full list of configuration options use ``oceand -help``.



Quick reference
---------------

The following RPCs are unique to the Ocean client

Wallet
^^^^^^


* `dumpderivedkeys <#dumpderivedkeys>`_
* `validatederivedkeys <#validatederivedkeys>`_
* `dumpkycfile <#dumpkycfile>`_
* `readkycfile <#readkycfile>`_
* `createkycfile <#createkycfile>`_
* `getderivedkeys <#getderivedkeys>`_
* `getcontract <#getcontract>`_
* `getcontracthash <#getcontracthash>`_
* `getmappinghash <#getmappinghash>`_
* `getethaddress <#getethaddress>`_
* `getethpeginaddress <#getethpeginaddress>`_
* `getethpegin <#getethpegin>`_
* `createrawethpegin <#createrawethpegin>`_
* `validateethpegin <#validateethpegin>`_
* `claimethpegin <#claimethpegin>`_
* `sendtoethmainchain <#sendtoethmainchain>`_
* `sendanytoaddress <#sendanytoaddress>`_

Utility
^^^^^^^


* `getutxoassetinfo <#getutxoassetinfo>`_
* `createrawissuance <#createrawissuance>`_
* `createrawreissuance <#createrawissuance>`_
* `createrawburn <#createrawburn>`_
* `testmempoolaccept <#testmempoolaccept>`_
* `createrawpolicytx <#createrawpolicytx>`_
* `createrawrequesttx <#createrawrequesttx>`_
* `getrequests <#getrequests>`_

Policy
^^^^^^


* `addtowhitelist <#addtowhitelist>`_
* `addmultitowhitelist <#addmultitowhitelist>`_
* `readwhitelist <#readwhitelist>`_
* `querywhitelist <#querywhitelist>`_
* `removefromwhitelist <#removefromwhitelist>`_
* `clearwhitelist <#clearwhitelist>`_
* `dumpwhitelist <#dumpwhitelist>`_
* `sendaddtowhitelisttx <#sendaddtowhitelisttx>`_
* `sendaddmultitowhitelisttx <#sendaddmultitowhitelisttx>`_
* `addtofreezelist <#addtofreezelist>`_
* `queryfreezelist <#queryfreezelist>`_
* `removefromfreezelist <#removefromfreezelist>`_
* `clearfreezelist <#clearfreezelist>`_
* `addtoburnlist <#addtoburnlist>`_
* `queryburnlist <#queryburnlist>`_
* `removefromburnlist <#removefromburnlist>`_
* `clearburnlist <#clearburnlist>`_



Configuration options
^^^^^^^^^^^^^^^^^^^^^

* `pkhwhitelist <#pkhwhitelist>`_
* `freezelist <#freezelist>`_
* `burnlist <#burnlist>`_
* `issuanceblock <#issuanceblock>`_
* `disablect <#disablect>`_
* `embedcontract <#embedcontract>`_
* `attestationhash <#attestationhash>`_
* `embedmapping <#embedmapping>`_
* `issuecontrolscript <#issuecontrolscript>`_
* `policycoins <#policycoins>`_
* `initialfreecoinsdestination <#initialfreecoinsdestination>`_
* `freezelistcoinsdestination <#initialfreecoinsdestination>`_
* `burnlistcoinsdestination <#initialfreecoinsdestination>`_
* `issuancecoinsdestination <#initialfreecoinsdestination>`_
* `permissioncoinsdestination <#permissioncoinsdestination>`_
* `mainchainrpchost <#>`_
* `mainchainrpcport <#>`_
* `validatepegin <#>`_
* `parentgenesisblockhash <#>`_
* `parentcontract <#>`_
* `fedpegaddress <#>`_
* `peginconfirmationdepth <#>`_

dumpderivedkeys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dumpderivedkeys`` RPC outputs a list of all contract tweaked
addresses in the key pool
along with the corresponding non-tweaked basis public keys to a specified file.

*Parameter #1---the filename of the output file*


.. raw:: html

   <table>
   <thead>
    <tr>
     <th>Name</th>
     <th>Type</th>
     <th>Presence</th>
     <th>Description</th>
    </tr>
   </thead>
   <tbody>
    <tr>
     <td>filename</td>
     <td>String</td>
     <td>Required<br />(exactly 1)</td>
     <td markdown="block">

     The name of the output file for the list of tweaked addresses and base public keys

     </td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>filename</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>The name of the output file for the list of tweaked addressed and base public keys</td>
     </tr>
    </tbody>
   </table>


*Result---nothing if valid keys, RPC errors if invalid keys found*

*Example*

.. code-block:: text

   ocean-cli validatederivedkeys


dumpkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``dumpkycfile`` RPC outputs an encrypted list of wallet tweaked public keys. A timestamp and best block hash of when the file was constructed is included.

*Parameter #1---the filename of the output file*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>filename</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The name of the output file for the encrypted list of tweaked public keys</td>
     </tr>
    </tbody>
   </table>

*Parameter #2---the public key with which to encrypt*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>onboardpubkey</td>
      <td>String (hex)</td>
      <td>Optional<br /> (0 or 1)</td>
      <td>The specific public key to be used for file encryption</td>
     </tr>
    </tbody>
   </table>
   
*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli dumpkycfile dumpfile.txt 1CDXUtbF3bBtritydFMKhRbbYhxDgCF5oH
   
   
readkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``readkycfile`` RPC reads in an encrypted list of tweaked public keys (as produced by `dumpkycfile <#dumpkycfile>`_\) from a specified file, and outputs the unencrypted list of tweaked addresses along with their corresponding non-tweaked public keys.

*Parameter #1---the encrypted filename*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>filename</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The name of the file containing the encrypted tweaked public key</td>
     </tr>
    </tbody>
   </table>
   
*Parameter #2---the filename of the output file*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>outfilename</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The name of the output file for the list of tweaked public keys and coresponding addresses</td>
     </tr>
    </tbody>
   </table>

*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli readkycfile dumpfile.txt dumpfileout.txt



createkycfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createkycfile`` RPC creates an encrypted kyc file that stores p2pkh and p2sh address data to be whitelisted when onboarding.

*Parameter #1---the created KYC file name*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>filename</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Name of the KYC file</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---P2PKH data for whitelisting in an onboarding transaction*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>pubkeylist</td>
      <td>Array of Objects</td>
      <td>Required<br />(1 or more)</td>
      <td>Contains tweaked addresses and respective untweaked public keys</td>
     </tr>
    </tbody>
   </table>


*Parameter #3---P2SH data for whitelisting in an onboarding transaction*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>multisiglist</td>
      <td>Array of Objects</td>
      <td>Required<br />(1 or more)</td>
      <td>Contains multisig metadata such as number of required signatures and arrays of untweaked public keys</td>
     </tr>
    </tbody>
   </table>


*Parameter #4---the public key issued by the server for onboarding encryption.*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>onboardpubkey</td>
      <td>String (hex)</td>
      <td>Optional<br />(exactly 1)</td>
      <td>Public key that is used for onboarding encryption</td>
     </tr>
    </tbody>
   </table>

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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>result
      </td>
      <td>Object</td>
      <td>Required<br />(exactly 1)</td>
      <td>An object containing a list of contract tweaked addresses and
   basis public keys</td>
     </tr>
     <tr>
      <td markdown="block">
      →<br>`address`
      </td>
      <td>string (hex)</td>
      <td>Required<br />(key pool size)</td>
      <td>Base58check encoded address corresponding to the contract tweaked public key</td>
     </tr>
     <tr>
      <td markdown="block">
      →<br>`bpubkey`
      </td>
      <td>String (hex)</td>
      <td>Required<br />(key pool size)</td>
      <td>Hex encoding of the compressed untweaked public key</td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>contract</td>
      <td>Object</td>
      <td>Required<br />(exactly 1)</td>
      <td>A JSON object containing the plain text of the contract</td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>blockheight</td>
      <td>Number (int)</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Block height to retrieve the contract hash from (Default: most recent block)</td>
     </tr>
    </tbody>
   </table>


*Result---the contract hash*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>contracthash</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The hex-encoded hash of the contract hash</td>
     </tr>

    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>blockheight</td>
      <td>Number (int)</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Block height to retrieve the mapping hash from (Default: most recent block)</td>
     </tr>
    </tbody>
   </table>


*Result---the mapping hash*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>mapping</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The hex-encoded hash of the mapping hash</td>
     </tr>

    </tbody>
   </table>


*Example*

.. code-block:: bash

   ocean-cli getmappinghash

Result:

.. code-block:: text

   f4f30db53238a7529bc51fcda04ea22bd8f8b188622a6488da12281874b71f72

getethaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getethaddress`` RPC returns an ethereum address from an EC private key.

*Parameter #1 ---private key*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>key</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>private key to generate Ethereum address from</td>
     </tr>
    </tbody>
   </table>
 
*Result---ethereum address if sucessful, RPC error if invalid data private key given*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum address for given private key</td>
     </tr>
    </tbody>
   </table>
   
 
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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>key</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>private key to generate Ethereum address and claim public key from</td>
     </tr>
    </tbody>
   </table>

*Result---JSON object containing Ethereum address and corresponding public key, or RPC error if unvalid private key given*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>eth_mainchain_address</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum address for given private key</td>
     </tr>
     <tr>
      <td>eth_claim_pubkey</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Claim Public key for given private key</td>
     </tr>
    </tbody>
   </table>
 
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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction id</td>
     </tr>
    </tbody>
   </table>
   
*Result---the transaction in JSON format, or RPC error if given transaction is invalid*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>result</td>
      <td>JSON</td>
      <td>Required<br />(exactly 1)</td>
      <td>The resulting transaction </td>
     </tr>
    </tbody>
   </table>
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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction id</td>
     </tr>
    </tbody>
   </table>

*Parameter #2 --- eth transaction peg-in amount*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>amount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction amount</td>
     </tr>
    </tbody>
   </table>

*Parameter #3 --- claim pubkey generated by getethpeginaddress*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>claim_pubkey</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Claim public key</td>
     </tr>
    </tbody>
   </table>

*Result---ethereum address, or RPC error if invalid claim public key given*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum address for given private key</td>
     </tr>
    </tbody>
   </table>
   
*Example*

.. code-block:: bash

   ocean-cli createrawethpegin 8b75539cc2b54efe15cd3a0f678545e3f154ca69ba87004d484d10eeb1359cc7 432.109 03220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a

Result:

.. code-block:: text

    0200000001016ca60fb08c36a2e77e0810de32181b63e8250fbf9a398f9bf9e53444cbf680300000004000ffffffff0201bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a01000003a3529429a8001976a914923e4d01599a5f200ad0527dc046e85bc4d19a5888ac01bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a010000000000001658000000000000000000050800409452a303000020bfe394bdcd72be5291a04263713c8c79d5d2ab6d45c9e7e5b08af475240a1d4a20a38fcbb10d8cec9ae6346a90d018a14567f5d5406ab810c0f8ae76f84067e5d42103220271a8833566153dbfa52c4ba13d2e56970885e6178a4ce6fa81ecaf38c35a206ca60fb08c36a2e77e0810de32181b63e8250fbf9a398f9bf9e53444cbf6803000000000

validateethpegin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``validateethpegin`` RPC validates an eth ERC-20 transaction to be used from peg-in to Ocean.


*Parameter #1 --- eth transaction id*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction id</td>
     </tr>
    </tbody>
   </table>
   
*Parameter #2 --- eth transaction peg-in amount*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>amount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction amount</td>
     </tr>
    </tbody>
   </table>

*Parameter #3 --- claim pubkey generated by getethpeginaddress*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>claim_pubkey</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Claim public key</td>
     </tr>
    </tbody>
   </table>
   
*Result---TRUE or FALSE*

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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction id</td>
     </tr>
    </tbody>
   </table>

*Parameter #2 --- eth transaction peg-in amount*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>amount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-in transaction amount</td>
     </tr>
    </tbody>
   </table>

*Parameter #3 --- claim pubkey generated by getethpeginaddress*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>claim_pubkey</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Claim public key</td>
     </tr>
    </tbody>
   </table>
   
*Result---ethereum transaction ID, or RPC error if invalid inputs given*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum transaction ID</td>
     </tr>
    </tbody>
   </table>

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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Destination Ethereum address for funds</td>
     </tr>
    </tbody>
   </table>

*Parameter #2 --- eth amount pegged-out to eth mainchain*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>amount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum peg-out amount</td>
     </tr>
    </tbody>
   </table>

*Parameter #3 --- Fee deducted from amount being pegged-out*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>subtractfeefromamount</td>
      <td>Boolean</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Whether to deduct fee from peg-out amount</td>
     </tr>
    </tbody>
   </table>
   
*Result---ethereum transaction ID of resulting sidechain transaction, or RPC error if invalid inputs given*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Ethereum transaction ID</td>
     </tr>
    </tbody>
   </table>


*Example*

.. code-block:: bash

   ocean-cli sendtoethmainchain 8e8a0ec05cc3c2b8511aabadeeb821df19ea7564 533.22 false

Result:

.. code-block:: text

    aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15

sendanytoaddress
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendanytoaddress`` RPC sends a combination of any non-policy assets to the address. The cumulative sum of the assets is equal to the desired amount. This rpc should only used in chains that are comprised of non-policy assets which are fungible.


*Parameter #1 --- destination address*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Destination address </td>
     </tr>
    </tbody>
   </table>

*Parameter #2 --- amount to be sent to the destination*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>amount</td>
      <td>Amount </td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount to be sent </td>
     </tr>
    </tbody>
   </table>

*Parameter #3 --- A comment used to store what the transaction is for*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>comment</td>
      <td>String</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Comment to store transaction purpose </td>
     </tr>
    </tbody>
   </table>

*Parameter #4 --- (string, optional) A comment to store the name of the person or organization to which you're sending the transaction*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>comment_to</td>
      <td>String</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Comment to store name of recipient </td>
     </tr>
    </tbody>
   </table>

*Parameter #5 --- Return a transaction even when a blinding attempt fails due to number of blinded inputs/outputs if this is set to true*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>ignoreblindfail</td>
      <td>Boolean</td>
      <td>Optional<br />(0 or 1)</td>
      <td>If true return a transaction even if blinding attempt fails</td>
     </tr>
    </tbody>
   </table>

*Parameter #6 --- Split a transaction that goes over the size limit into smaller transactions if this is set to true*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>splitlargetxs</td>
      <td>Boolean</td>
      <td>Optional<br />(0 or 1)</td>
      <td>If true split a transaction that goes over the size limit</td>
     </tr>
    </tbody>
   </table>

*Parameter #7 --- Choose which balances should be used first. 1 - descending, 2 - ascending*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>balanceSortType</td>
      <td>Numeric</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Specify which balance to use first</td>
     </tr>
    </tbody>
   </table>

*Result---txid of generated transaction, or RPC error if failure*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Constructed transaction txid</td>
     </tr>
    </tbody>
   </table>

*Example*

.. code-block:: bash

   ocean-cli sendtanytoaddress 8e8a0ec05cc3c2b8511aabadeeb821df19ea7564 533.22

Result:

.. code-block:: text

    aa2364284941f08cceaf49911858125256d61f1b728e544ead6423bf06ea1e15


getutxoassetinfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getutxoassetinfo`` RPC returns a summary of the total amounts of unspent (and un-burnt)
assets in the UTXO set. Amounts in transactions marked as frozen (i.e. with one output
having a zero address) are listed in a separate field.

*Parameters: none*

*Result---an array of JSON objects containing the unspent amounts for each issued asset*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>contract</td>
      <td>object</td>
      <td>Required<br />(exactly 1)</td>
      <td>An array of JSON objects of unspent amounts for each issued asset</td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>assetaddress</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check issued asset address</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---the amount of issued asset*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>AssetAmount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of asset to issue</td>
     </tr>
    </tbody>
   </table>


*Parameter #3---the Base58check address for the reissuance token*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>tokenaddress</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check reissuance token address</td>
     </tr>
    </tbody>
   </table>


*Parameter #4---the amount of reissuance token*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>TokenAmount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of reissuance token</td>
     </tr>
    </tbody>
   </table>


*Parameter #5---the Base58check address for the issuanceAsset change*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>ChangeAddress</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check change address</td>
     </tr>
    </tbody>
   </table>


*Parameter #6---the amount of issuanceAsset change*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>ChangeAmount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of issuanceAsset to return per output</td>
     </tr>
    </tbody>
   </table>


*Parameter #7---the number of issuanceAsset outputs*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>NumChange</td>
      <td>Integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>Numebr of issuanceAsset outputs</td>
     </tr>
    </tbody>
   </table>


*Parameter #8---input TXID*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>InputTxid</td>
      <td>String (hex)</td>
      <td>Required<br />(exactly 1)</td>
      <td>Hex of the input TXID containing the issuanceAsset</td>
     </tr>
    </tbody>
   </table>


*Parameter #9---input transaction vout*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>vout</td>
      <td>Integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>Input transaction vout</td>
     </tr>
    </tbody>
   </table>


*Result---the unsigned raw transaction in hex*

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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>AssetAddress</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check re-issued asset address</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---the amount of re-issued asset*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>AssetAmount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of asset to re-issue</td>
     </tr>
    </tbody>
   </table>


*Parameter #3---the Base58check address for the return reissuance token*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>TokenAddress</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check reissuance token address</td>
     </tr>
    </tbody>
   </table>


*Parameter #4---the amount of reissuance token to return*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>TokenAmount</td>
      <td>Amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of reissuance token</td>
     </tr>
    </tbody>
   </table>


*Parameter #5---input TXID*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>inputtxid</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Hex of the input TXID containing the re-issuance token</td>
     </tr>
    </tbody>
   </table>


*Parameter #6---input transaction vout*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>vout</td>
      <td>Integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>Input transaction vout</td>
     </tr>
    </tbody>
   </table>


*Parameter #7---the asset entropy*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Entropy</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The hex encoded asset entropy</td>
     </tr>
    </tbody>
   </table>


*Result---the unsigned raw transaction in hex*

*Example*

.. code-block:: bash

   ocean-cli createrawissuance 2deJ6F3w6HUtXM8JjY5YPc8wtaXerqFX7HA 0.5 2ddSmTujABoCzDyU9hPghcp4ojAGmJRtnWk 1.00 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a 0 b2e15d0d7a0c94e4e2ce0fe6e8691b9e451377f6e46e8045a86f7c4b5d4f0f23

Result:

.. code-block:: text

   0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754d4c0040174820afc79a50ed4b9de7bfbc286adead7d46300787e3d986dd8bff570902d620100000002dd231b00001976a9143565dfe051b252c6c5d51275fa91850ef0bb26b288ac017c2096b20b81bd5734cb631335fa1a5bfed9c5a10e2ba4ce86df1b28fb2d50e401000000000754d4c0001976a9142c11d12a57cdd294b3ffa89cf2503a612252fbf288ac01230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b20100000007c0d4e9b00017a91458d6453537165062f887d004b979b054c5fa98c28701230f4f5d4b7c6fa845806ee4f67713459e1b69e8e60fcee2e4940c7a0d5de1b201000000000000c350000000000000

createrawburn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawburn`` RPC creates a raw unsigned burn (OP_RETURN) transaction with a single input and single output.

*Parameter #1---input TXID*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Hex of the input TXID containing the burn asset</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---input transaction vout*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>vout</td>
      <td>Integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>Input transaction vout</td>
     </tr>
    </tbody>
   </table>


*Parameter #3---the asset type*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Asset</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The hex encoded asset id to be burnt</td>
     </tr>
    </tbody>
   </table>


*Parameter #4---the amount to burn (must equal the input amount)*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>burnamount</td>
      <td>amount</td>
      <td>Required<br />(exactly 1)</td>
      <td>Amount of burnt asset</td>
     </tr>
    </tbody>
   </table>


*Result---the unsigned raw transaction in hex*

*Example*

.. code-block:: bash

   ocean-cli createrawissuance 40ac4e02a64ea14190e96d6e1c5c877b12522db3fb5adffd58b1aed0cc11150a 0 b2e15d0d7a0c94e4e2ce0fe6e8691b9e451377f6e46e8045a86f7c4b5d4f0f23 0.342100

Result:

.. code-block:: text

   0200000000010a1511ccd0aeb158fddf5afbb32d52127b875c1c6e6de99041a14ea6024eac400000008000ffffffff000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000002dd231b0001000000000754dee2e4940c7a0d5de1b201000000000000c350000000000000

createrawrequesttx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``createrawrequesttx`` RPC creates a raw request transaction with a single input and single output.

*Parameter #1---Input object with details on transaction to be spent*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
     <tr>
      <td markdown="span">
      Input
      </td>
      <td markdown="span">
      object
      </td>
      <td markdown="span">
      Required<br>(1 or more)
      </td>
      <td markdown="span">
      An object identifying an input transaction
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`txid`
      </td>
      <td markdown="span">
      string (hex)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The txid of the input transaction
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`vout`
      </td>
      <td markdown="span">
      number (int)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The output index number (vout) of input transaction
      </td>
     </tr>
    </tbody>
   </table>

*Parameter #2---Output object with request details*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
     <tr>
      <td markdown="span">
      Output
      </td>
      <td markdown="span">
      object
      </td>
      <td markdown="span">
      Required<br>(1 or more)
      </td>
      <td markdown="span">
      An object identifying an output
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`pubkey`
      </td>
      <td markdown="span">
      string (hex)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Public key of the output transaction
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`decayConst`
      </td>
      <td markdown="span">
      Integer
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Speed at which tickets value decays
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`endBlockHeight`
      </td>
      <td markdown="span">
      Integer
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      End block of ticket sale window 
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`fee`
      </td>
      <td markdown="span">
     Amount
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Size of fee
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`genesisBlockHash`
      </td>
      <td markdown="span">
      String 
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Hash of genesis block
      </td>
     </tr>
     <tr>
      <td markdown="span">
      →<br>`startBlockHeight`
      </td>
      <td markdown="span">
      Integer
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Start block of ticket sale window 
      </td>
     </tr>
      <tr>
      <td markdown="span">
      →<br>`startPrice`
      </td>
      <td markdown="span">
      Amount
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Starting price of ticket
      </td>
     </tr>
      <tr>
      <td markdown="span">
      →<br>`tickets`
      </td>
      <td markdown="span">
      Integer
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Number of tickets to issue
      </td>
     </tr>
      <tr>
      <td markdown="span">
      →<br>`value`
      </td>
      <td markdown="span">
      Amount
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Value of tickets
      </td>
     </tr>
    </tbody>
   </table>

*Result---the raw transaction in hex*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      `result`
      </td>
      <td markdown="span">
      string
      </td>
      <td markdown="span">
      Required<br>(Exactly 1)
      </td>
      <td markdown="span">
      The resulting raw transaction in serialized transaction format, encoded as hex.  If the transaction couldn't be generated, this will be set to JSON `null` and the JSON-RPC error field may contain an error message
      </td>
     </tr>
    </tbody>
   </table>





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

testmempoolaccept
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``testmempoolaccept`` RPC determines the validity of a raw transaction without broadcasting it. It performs the exact same validity checks as performed on mempool acceptance, including locally configured policy rules, but without adding the transaction to the mempool.

*Parameter #1---signed raw transaction*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>rawtx</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>Hex encoded serialised transaction</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---accept large fee*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>nLargeFee</td>
      <td>Boolean</td>
      <td>Optional<br />(0 or 1)</td>
      <td>Allow large fee</td>
     </tr>
    </tbody>
   </table>


*Result---a JSON object containing the transaction ID, whether the transaction is accepted or rejected, and if rejected the reason*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>txid</td>
      <td>String</td>
      <td>Required<br />(exactly 1)</td>
      <td>The raw transaction ID</td>
     </tr>
    </tbody>
    <tbody>
     <tr>
      <td>allowed</td>
      <td>boolean</td>
      <td>Required<br />(exactly 1)</td>
      <td>The determination on whether the transaction is valid/allowed</td>
     </tr>
    </tbody>
    <tbody>
     <tr>
      <td>reject-reason</td>
      <td>String</td>
      <td>Optional<br />(0 or 1)</td>
      <td>The reason if the transaction would be rejected</td>
     </tr>
    </tbody>
   </table>


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

The ``createrawpolicytx`` RPC creates a raw unsigned policy transaction that encodes an address to be added to a policy list. To be accepted, the asset type must match the policy asset type as defined in the genesis block (via ``-freezelistcoinsdestination`` and ``-burnlistcoinsdestination``. The policy asset input(s) are specified in an array, and the outputs are specified in an array of objects that contain a policy public key, the address to be added to to the policy list and the value. Spending these outputs removes the addresses from the policy lists.

*Parameter #1---Inputs*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      Inputs
      </td>
      <td markdown="span">
      array
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      An array of objects, each one to be used as an input to the transaction
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → Input
      </td>
      <td markdown="span">
      object
      </td>
      <td markdown="span">
      Required<br>(1 or more)
      </td>
      <td markdown="span">
      An object describing a particular input
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`txid`
      </td>
      <td markdown="span">
      string (hex)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The TXID of the outpoint to be spent encoded as hex in RPC byte order
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`vout`
      </td>
      <td markdown="span">
      number (int)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The output index number (vout) of the outpoint to be spent; the first output in a transaction is index `0`
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`Sequence`
      </td>
      <td markdown="span">
      number (int)
      </td>
      <td markdown="span">
      Optional<br>(0 or 1)
      </td>
      <td markdown="span">
      The sequence number to use for the input
      </td>
     </tr>
    </tbody>
   </table>


*Parameter #1---Addresses to add to policy lists and control keys*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      Policy output
      </td>
      <td markdown="span">
      array
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      An array of objects, each one is used to add an address to a policy list.
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → Input
      </td>
      <td markdown="span">
      object
      </td>
      <td markdown="span">
      Required<br>(1 or more)
      </td>
      <td markdown="span">
      An object encoding an output with a policy list address.
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`pubkey`
      </td>
      <td markdown="span">
      string (hex)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The public key of the policy authority wallet used to spend the output
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`value`
      </td>
      <td markdown="span">
      number (coins)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The amount of policy asset to be sent to the output
      </td>
     </tr>
     <tr>
      <td markdown="span">
      → →<br>`address`
      </td>
      <td markdown="span">
      string (base58check)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The address to be added to the policy list
      </td>
     </tr>
    </tbody>
   </table>


*Parameter #3---locktime*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      Locktime
      </td>
      <td markdown="span">
      numeric (int)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      Indicates the earliest time (in block height or Unix epoch time) a transaction can be added to the block chain
      </td>
     </tr>
    </tbody>
   </table>


*Parameter #4---policy asset*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      Policy asset
      </td>
      <td markdown="span">
      string (hex)
      </td>
      <td markdown="span">
      Required<br>(exactly 1)
      </td>
      <td markdown="span">
      The 32 byte asset ID of the policy asset of the list being updated
      </td>
     </tr>
    </tbody>
   </table>


*Result---the unsigned raw transaction in hex*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td markdown="span">
      `result`
      </td>
      <td markdown="span">
      string
      </td>
      <td markdown="span">
      Required<br>(Exactly 1)
      </td>
      <td markdown="span">
      The resulting unsigned raw transaction in serialized transaction format, encoded as hex.  If the transaction couldn't be generated, this will be set to JSON `null` and the JSON-RPC error field may contain an error message
      </td>
     </tr>
    </tbody>
   </table>


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


getrequests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``getrequests`` RPC returns all the active client requests in the blockchain.

*Parameter #1---the client genesis block hash*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>genesis block hash</td>
      <td>hash (string)</td>
      <td>Optional</td>
      <td>The genesis block hash to filter requests by</td>
     </tr>
    </tbody>
   </table>


*Result---an array of JSON objects containing details for each active request*

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

addtowhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addtowhitelist`` RPC adds a valid contract tweaked address to the node
mempool whitelist. It requires both an address and corresponding base public
key, and the RPC checks that the address is valid and has been tweaked
from the supplied base public key with the current
contract hash as present in the most recent block header.

*Parameter #1---the Base58check contract tweaked address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded contract tweaked address</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---the base (un-tweaked) compressed public key*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>bpubkey</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Hex encoded base public key</td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>tweakedaddress</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded contract tweaked p2sh address</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---the base (un-tweaked) compressed public keys that the p2sh was created with*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>basepubkeys</td>
      <td>array</td>
      <td>Required<br />(1 or more)</td>
      <td>Hex encoded base public keys</td>
     </tr>
    </tbody>
   </table>

*Parameter #3---the n of Multisig*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>nmultisig</td>
      <td>integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>number of signatures required for multisig</td>
     </tr>
    </tbody>
   </table>

*Parameter #4---the Base58 KYC address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>kycaddress</td>
      <td>string</td>
      <td>Optional<br />(exactly 1)</td>
      <td>Base58 KYC address</td>
     </tr>
    </tbody>
   </table>


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addmultitowhitelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz [028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58,028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58] 1

querywhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``querywhitelist`` RPC queries if a specified address is present in the node mempool whitelist.

*Parameter #1---the Base58check encoded address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


*Result---TRUE of FALSE*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>output</td>
      <td>boolian</td>
      <td>Required<br />(exactly 1)</td>
      <td>1 is the address is present, 0 otherwise</td>
     </tr>

    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>filename</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Name of file containing the list of contract tweaked address and base public keys</td>
     </tr>
    </tbody>
   </table>


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli readwhitelist derivedkeys.txt

removefromwhitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``removefromwhitelist`` RPC removes a specified address from the node mempool whitelist.

*Parameter #1---the Base58check encoded address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
   <thead>
    <tr>
     <th>Name</th>
     <th>Type</th>
     <th>Presence</th>
     <th>Description</th>
    </tr>
   </thead>
   <tbody>
    <tr>
     <td>filename</td>
     <td>string</td>
     <td>Required<br />(exactly 1)</td>
     <td markdown="block">

     The name of the output file for the list of whitelist addresses

     </td>
     </tr>
    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>tweakedaddress</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded contract tweaked p2sh address</td>
     </tr>
    </tbody>
   </table>


*Parameter #2---the base (un-tweaked) compressed public keys that the p2sh was created with*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>basepubkeys</td>
      <td>array</td>
      <td>Required<br />(1 or more)</td>
      <td>Hex encoded base public keys</td>
     </tr>
    </tbody>
   </table>

*Parameter #3---the n of Multisig*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>nmultisig</td>
      <td>integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>number of signatures required for multisig</td>
     </tr>
    </tbody>
   </table>

*Parameter #4---the fee asset type*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>feeasset</td>
      <td>string</td>
      <td>Optional<br />(exactly 1)</td>
      <td>Type of the fee asset</td>
     </tr>
    </tbody>
   </table>


*Result---transaction hex if valid, errors returned if invalid inputs or null if wallet is not working*

*Example*

.. code-block:: bash

   ocean-cli sendaddmultitowhitelisttx 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz [028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58,028f9c608ded55e89aef8ade69b90612510dbd333c8d63cbe1072de9049731bb58] 1

sendaddtowhitelisttx
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``sendaddtowhitelisttx`` RPC serializes and sends an OP_REGISTERADDRESS transaction which is used to add valid contract tweaked addresses to the whitelist (they are automatically retrieved from the wallet pool). Whitelist node reads the transaction and adds the addresses to a whitelist if details are valid.

*Parameter #1---Number of addresses that should be taken from the wallet pool and whitelisted*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>naddresses</td>
      <td>integer</td>
      <td>Required<br />(exactly 1)</td>
      <td>Number of addresses to register</td>
     </tr>
    </tbody>
   </table>

*Parameter #2---the fee asset type*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>feeasset</td>
      <td>string</td>
      <td>Optional<br />(exactly 1)</td>
      <td>Type of the fee asset</td>
     </tr>
    </tbody>
   </table>


*Result---transaction hex if valid, null if wallet is not working*

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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check address</td>
     </tr>
    </tbody>
   </table>


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addtofreezelist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz

queryfreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``queryfreezelist`` RPC queries if a specified address is present in the node mempool freezelist.

*Parameter #1---the Base58check encoded address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


*Result---TRUE of FALSE*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>output</td>
      <td>boolian</td>
      <td>Required<br />(exactly 1)</td>
      <td>1 is the address is present, 0 otherwise</td>
     </tr>

    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


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

addtofreezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``addtoburnlist`` RPC adds an address to the node
mempool burnlist. Transactions spending from UTXOs with
output addresses on the freezelist are alowed into the
mempool if these addresses are also on the burnlist and have
only TX_FEE and TX_NULL_DATA outputs (with both the ``-freezelist``
and ``-burnlist`` configurations options enabled).

*Parameter #1---the Base58check address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check address</td>
     </tr>
    </tbody>
   </table>


*Result---none if valid, errors returned if invalid inputs*

*Example*

.. code-block:: bash

   ocean-cli addtoburnlist 2dZhhVmJkXCaWUzPmhmwQ3gBJm2NJSnrvyz

queryburnlist
^^^^^^^^^^^^^^

The ``queryburnlist`` RPC queries if a specified address is present in the node mempool burnlist.

*Parameter #1---the Base58check encoded address*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


*Result---TRUE of FALSE*


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>output</td>
      <td>boolian</td>
      <td>Required<br />(exactly 1)</td>
      <td>1 is the address is present, 0 otherwise</td>
     </tr>

    </tbody>
   </table>


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


.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Presence</th>
      <th>Description</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>address</td>
      <td>string</td>
      <td>Required<br />(exactly 1)</td>
      <td>Base58check encoded address</td>
     </tr>
    </tbody>
   </table>


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

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Type</th>
      <th>Default</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Boolean</td>
      <td>0</td>
     </tr>
    </tbody>
   </table>

*Example---The following examples enable whitelisting*

.. code-block:: bash

   oceand -pkhwhitelist
   
In ocean.conf:
   pkhwhitelist=1
   

freezelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables node mempool address freezelisting. 

*Argument---TRUE or FALSE*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Type</th>
      <th>Default</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Boolean</td>
      <td>0</td>
     </tr>
    </tbody>
   </table>

*Example---The following examples enable freezelisting*

.. code-block:: bash

   oceand -freezelist
   
In ocean.conf:
   freezelist=1


burnlist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables node mempool address burnlisting. 

*Argument---TRUE or FALSE*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Type</th>
      <th>Default</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Boolean</td>
      <td>0</td>
     </tr>
    </tbody>
   </table>

*Example---The following examples enable burnlisting*

.. code-block:: bash

   oceand -burnlist
   
In ocean.conf:
   burnlist=1
   
   
issuanceblock
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Enables blocking of invalid issuance transactions from mempool. Checks that asset issuance transactions have an issuanceAsset input.

*Argument---TRUE or FALSE*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Type</th>
      <th>Default</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Boolean</td>
      <td>0</td>
     </tr>
    </tbody>
   </table>

*Example---The following examples enable issuance blocking*

.. code-block:: bash

   oceand -issuanceblock
   
In ocean.conf:
   issuanceblock=1
   
   
disablect
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Disables confidential transactions and addresses.

*Argument---TRUE or FALSE*

.. raw:: html

   <table>
    <thead>
     <tr>
      <th>Type</th>
      <th>Default</th>
     </tr>
    </thead>
    <tbody>
     <tr>
      <td>Boolean</td>
      <td>0</td>
     </tr>
    </tbody>
   </table>

*Example---The following examples enables disablect

.. code-block:: bash

   oceand -disablect
   
In ocean.conf:
   disablect=1
