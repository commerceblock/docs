Mainstay connector service API
------------------------------

The Mainstay connector service includes a public API to both perform attestations of state and retrieve slot proofs 
and sequence proofs (PoIS). To commit data to a slot requires an API token, which is provided after sign-up on `mainstay.xyz` 
an allocation of the slot position (additionally a signature can be required with the commitment, with the user public key 
supplied at sign-up). 

REST framework structure
^^^^^^^^^^^^^^^^^^^^^^^^

::

   response = json response object

   response['error'] : json response error field

   timestamp : timestamp in ms

   allowance : time taken to respond in ns

Public Endpoints
^^^^^^^^^^^^^^^^

Index
~~~~~

API index page.

**request:** https://mainstay.xyz/api/v1

**response:**

.. code-block:: json

   {
       "response": "Mainstay-API-v1",
       "timestamp": 1548329067489,
       "allowance":
       {
           "cost": 4562
       }
   }

Latest Attestation
~~~~~~~~~~~~~~~~~~

Provide information on latest Merkle root commitment to the staychain.

**request:** https://mainstay.xyz/api/v1/latestattestation

**response:**

.. code-block:: json

   {
       "response":
       {
           "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
           "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13"
       },
       "timestamp": 1548329116999,
       "allowance":
       {
           "cost": 1796883
       }
   }

Latest Commitment
~~~~~~~~~~~~~~~~~

Provide information on latest commitment for a specific slot position.

**request:** https://mainstay.xyz/api/v1/latestcommitment?position=3

**response:**

.. code-block:: json

   {
       "response":
       {
           "commitment": "d235db29356bb02f37e16712c4d34a724282fd81134fbfda61407b3009755a9e",
           "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
           "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13"
       },
       "timestamp": 1548329166363,
       "allowance":
       {
           "cost": 3119659
       }
   }

Commitment
~~~~~~~~~~

Fetch commitment information for a specific slot position and merkle_root.

**request:** https://mainstay.xyz/api/v1/commitment?position=3&merkle_root=f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b

**response:**

.. code-block:: json

   {
       "response":
       {
           "commitment": "d235db29356bb02f37e16712c4d34a724282fd81134fbfda61407b3009755a9e",
           "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b"
       },
       "timestamp": 1548329204516,
       "allowance":
       {
           "cost": 1484074
       }
   }

Commitment Latest Proof
~~~~~~~~~~~~~~~~~~~~~~~

Fetch latest commitment proof for a specific slot position.

**request:** https://mainstay.xyz/api/v1/commitment/latestproof?position=1

**response:**

.. code-block:: json

   {
       "response":
       {
           "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13",
           "commitment": "d235db29356bb02f37e16712c4d34a724282fd81134fbfda61407b3009755a9e",
           "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
           "ops": [
           {
               "append": false,
               "commitment": "5309053b9d4db8f86d2c7ec164645bdf1669111280e49e04c036c323b58f4709"
           },
           {
               "append": false,
               "commitment": "213e122aaec314a94f111dd8dc797814660b680f7258f1d95adec56318eabd7c"
           },
           {
               "append": true,
               "commitment": "406ab5d975ae922753fad4db83c3716ed4d2d1c6a0191f8336c76000962f63ba"
           }]
       },
       "timestamp": 1548330374527,
       "allowance":
       {
           "cost": 19732506
       }
   }

Commitment Verify
~~~~~~~~~~~~~~~~~

Check if a commitment for a specific slot position is included in an Merkle root.

**request:** https://mainstay.xyz/api/v1/commitment/verify?position=1&commitment=5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053

**response:**

.. code-block:: json

   {
       "response":
       {
           "confirmed": true
       },
       "timestamp": 1548329867868,
       "allowance":
       {
           "cost": 30212539
       }
   }

Commitment Proof
~~~~~~~~~~~~~~~~

Get the merkle commitment proof (*slot proof*) for a specific slot position and merkle root.

**request:** https://mainstay.xyz/api/v1/commitment/proof?position=1&merkle_root=f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b

**response:**

.. code-block:: json

   {
       "response":
       {
           "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
           "commitment": "5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053",
           "ops": [
           {
               "append": false,
               "commitment": "21b0a66806bdc99ac4f2e697d05cb17c757ae10deb851ee869830d617e4f519c"
           },
           {
               "append": true,
               "commitment": "622d1b5efe11e9031f1b25aac11587e0ff81a37e9565ded16ee8e82bbc0c2fc1"
           },
           {
               "append": true,
               "commitment": "406ab5d975ae922753fad4db83c3716ed4d2d1c6a0191f8336c76000962f63ba"
           }]
       },
       "timestamp": 1548330450896,
       "allowance":
       {
           "cost": 2098095
       }
   }

Commitment Data
~~~~~~~~~~~~~~~~~~~~~

Get staychain information on a specific commitment.

**request:** https://mainstay.xyz/api/v1/commitment/commitment?commitment=5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053

**response:**

.. code-block:: json

   {
       "response":
       {
           "attestation":
           {
               "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
               "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13",
               "confirmed": true,
               "inserted_at": "16:06:41 23/01/19"
           },
           "merkleproof":
           {
               "position": 1,
               "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
               "commitment": "5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053",
               "ops": [
               {
                   "append": false,
                   "commitment": "21b0a66806bdc99ac4f2e697d05cb17c757ae10deb851ee869830d617e4f519c"
               },
               {
                   "append": true,
                   "commitment": "622d1b5efe11e9031f1b25aac11587e0ff81a37e9565ded16ee8e82bbc0c2fc1"
               },
               {
                   "append": true,
                   "commitment": "406ab5d975ae922753fad4db83c3716ed4d2d1c6a0191f8336c76000962f63ba"
               }]
           }
       },
       "timestamp": 1548330505898,
       "allowance":
       {
           "cost": 60414043
       }
   }

Merle Tree
~~~~~~~~~~

Get information on the commitments to a Merkle tree.

**request:** https://mainstay.xyz/api/v1/merkleroot?merkle_root=f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b

**response:**

.. code-block:: json

   {
       "response":
       {
           "attestation":
           {
               "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
               "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13",
               "confirmed": true,
               "inserted_at": "16:06:41 23/01/19"
           },
           "merkle_commitment": [
           {
               "position": 0,
               "commitment": "21b0a66806bdc99ac4f2e697d05cb17c757ae10deb851ee869830d617e4f519c"
           },
           {
               "position": 1,
               "commitment": "5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053"
           },
           {
               "position": 2,
               "commitment": "5309053b9d4db8f86d2c7ec164645bdf1669111280e49e04c036c323b58f4709"
           },
           {
               "position": 3,
               "commitment": "d235db29356bb02f37e16712c4d34a724282fd81134fbfda61407b3009755a9e"
           },
           {
               "position": 4,
               "commitment": "9b07569d4fd42ae3a19c0803b7401443e0275feb728e8103330d7d8615eecb62"
           }]
       },
       "timestamp": 1548330553639,
       "allowance":
       {
           "cost": 3318936
       }
   }

Slot Position
~~~~~~~~~~~~~~

Get information on a client slot position.

**request:** https://mainstay.xyz/api/v1/position?position=1

**response:**

.. code-block:: json

   {
       "response":
       {
           "position": [
           {
               "position": 1,
               "merkle_root": "300ab922905c67631e46e6d014be286fe1bb6dc550ae2df83484fcb1ccb21011",
               "commitment": "5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053",
               "ops": [
               {
                   "append": false,
                   "commitment": "2851174cf04f206e6fdfd78a9208c90a324fea5e97ee5b0629d35b5a853fbcfc"
               },
               {
                   "append": true,
                   "commitment": "622d1b5efe11e9031f1b25aac11587e0ff81a37e9565ded16ee8e82bbc0c2fc1"
               },
               {
                   "append": true,
                   "commitment": "406ab5d975ae922753fad4db83c3716ed4d2d1c6a0191f8336c76000962f63ba"
               }]
           },
           {
               "position": 1,
               "merkle_root": "2522e16722cfb1b29d01bbe6bfabe54ef7dd69b8bf8a00f911103284eebf4e3e",
               "commitment": "5555c29bc4ac63ad3aa4377d82d40460440a67f6249b463453ca6b451c94e053",
               "ops": [
               {
                   "append": false,
                   "commitment": "586f199625d902706e0ebf24e2720e62f3f4343a5d7b2ddc2fac155fb359ca3a"
               },
               {
                   "append": true,
                   "commitment": "622d1b5efe11e9031f1b25aac11587e0ff81a37e9565ded16ee8e82bbc0c2fc1"
               },
               {
                   "append": true,
                   "commitment": "406ab5d975ae922753fad4db83c3716ed4d2d1c6a0191f8336c76000962f63ba"
               }]
           }, ]
       },
       "timestamp": 1548330579389,
       "allowance":
       {
           "cost": 31613129
       }
   }

Attestation
~~~~~~~~~~~

Get information on an attestation.

**request:** https://mainstay.xyz/api/v1/attestation?txid=38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13

**response:**

.. code-block:: json

   {
       "response":
       {
           "attestation":
           {
               "merkle_root": "f46a58a0cc796fade0c7854f169eb86a06797ac493ea35f28dbe35efee62399b",
               "txid": "38fa2c6e103673925aaec50e5aadcbb6fd0bf1677c5c88e27a9e4b0229197b13",
               "confirmed": true,
               "inserted_at": "16:06:41 23/01/19"
           },
           "attestationInfo":
           {
               "txid": "86b372fb70e0935bfff4d6ba112e78cb9a3201ca15251dcd7db7cbf135b342b5",
               "amount": 149.9999155,
               "blockhash": "3c50145441751dfb8f01cd05f21a24d0763005334667daa734bbf4147eeabe14",
               "time": 1548253554
           }
       },
       "timestamp": 1548330644403,
       "allowance":
       {
           "cost": 7959634
       }
   }

Block
~~~~~

Get information on a Bitcoin block if it contains a Mainstay Merkle root commitment.

**request:** https://mainstay.xyz/api/v1/blockhash?hash=3c50145441751dfb8f01cd05f21a24d0763005334667daa734bbf4147eeabe14

**response:**

.. code-block:: json

   {
       "response":
       {
           "blockhash":
           {
               "txid": "86b372fb70e0935bfff4d6ba112e78cb9a3201ca15251dcd7db7cbf135b342b5",
               "amount": 149.9999155,
               "blockhash": "3c50145441751dfb8f01cd05f21a24d0763005334667daa734bbf4147eeabe14",
               "time": "14:25:54 23/01/19"
           }
       },
       "timestamp": 1548330671498,
       "allowance":
       {
           "cost": 1543490
       }
   }

Authenticated Endpoints
^^^^^^^^^^^^^^^^^^^^^^^

Commitment Send
~~~~~~~~~~~~~~~

**Node.js example**

.. code-block:: js

   const request = require('request');
   let elliptic = require('elliptic');
   let ec = new elliptic.ec('secp256k1');

   const url = "https://mainstay.xyz/api/v1";
   const route = '/commitment/send'
   const pubKey = '1CsSceq9GWnmozaky3DGa24UER6gRDgibf';
   const pvtKey =
       'bac52bbea2194e7ea1cd3da6585b66d28f1a7a3683eca91af4ba6373d323d24f';
   const commitment =
       'F01111111111111111111111111111111111111111111111111111111111110F';


   let keyPair = ec.keyFromPrivate("97ddae0f3a25b92268175400149d65d6887b9cefaf28ea2c078e05cdc15a3c0a");
   let privKey = keyPair.getPrivate("hex");
   let pubKey = keyPair.getPublic();

   let signature = ec.sign(commitment, privKey, "hex", {canonical: true}).toDER('base64');

   var payload = {
     commitment: commitment,
     position: 0,
     token: '4c8c006d-4cee-4fef-8e06-bb8112db6314',
   };

   payload = new Buffer(JSON.stringify(payload)).toString('base64');

   const options = {
     url: url + route,
     headers: {
       'X-MAINSTAY-PAYLOAD': payload,
       'X-MAINSTAY-SIGNATURE': signature
     }
   };

   request.post(options, (error, response, body) => {
     if (error)
       return console.log(error);
     ...
   });

**Curl example**

.. code-block:: perl

   curl --header "Content-Type: application/json" --request POST --data '{"X-MAINSTAY-PLAYLOAD":"eyJwb3NpdGlvbiI6MCwiY29tbWl0bWVudCI6IkYwMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMTExMEYifQ==","X-MAINSTAY-SIGNATURE":"IJbqe50XtfZbQ1b0jr+J1tswSPfZlWwZugXCpYbwYMPuRl+htqSb7wTLYY9RtQ6Bw9Ym5dw0vMNRaDwR8pked2Y="}' http://localhost:9000/api/v1/commitment/send

*response*

.. code-block:: perl

   {"response":"feedback","timestamp":1541761540171,"allowance":{"cost":4832691}}
