Coordinator daemon
==================

The Coordinator daemon is responsible for verifying the operation of Guardnodes in the Commerceblock Covalence system. It is ran by the authority controlling the service chain with the following functionality:

- Send guardnode challenges (request for specific piece of data from client chain to prove guardnode maintains a local copy of the client blockchain)
- Verify challenge responses (Verify that the response data is correct and is returned within a small enough period of time from when the challenge was sent)
- Send guardnode fee payments at the end of each request service period
- Query the connection status of the guardnode client chain node and obtain the current peer list


Running
-------
Information on running a Coordinator daemon can be found in the Github `repository <https://github.com/commerceblock/coordinator>`_.


Requirements
------------

To run the coordinator daemon we require:

* Commerce Block service chain connectivity to receive active client requests
* Client chain connectivity to generate guardnode challenges
* A DB instance to store requests and responses
* Listener HTTP POST endpoint to receive guardnode responses
* A public RPC API to offer information on requests and guardnode response performance


Configuration
-------------

Connection information for all services that the coordinator needs to interact with are set via enviroment variables:

+-----------------------------+----------------------------------------------------------------------------+
| Env variable                |  Decription                                                                |
+=============================+============================================================================+
| CO_LISTENER_HOST            | Host address to receive guardnode responses (default: localhost:80)        |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CHALLENGE_FREQUENCY      | Frequency in number of blocks that new challenges are created (default: 1) |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CHALLENGE_DURATION       | Challenge duration in seconds (default: 60)                                |
+-----------------------------+----------------------------------------------------------------------------+
| CO_BLOCK_TIME               | Service chain block time in seconds (default: 60)                          |
+-----------------------------+----------------------------------------------------------------------------+
| CO_LOG_LEVEL                | Environment logger log level (default: "coordinator")                      |
+-----------------------------+----------------------------------------------------------------------------+
| CO_API_HOST                 | RPC api host address                                                       |
+-----------------------------+----------------------------------------------------------------------------+
| CO_API_USER                 | RPC api username                                                           |
+-----------------------------+----------------------------------------------------------------------------+
| CO_API_PASS                 | RPC api password                                                           |
+-----------------------------+----------------------------------------------------------------------------+
| CO_STORAGE_HOST             | DB storage host address (default: "localhost:27017")                       |
+-----------------------------+----------------------------------------------------------------------------+
| CO_STORAGE_USER             | DB storage username                                                        |
+-----------------------------+----------------------------------------------------------------------------+
| CO_STORAGE_PASS             | DB storage password                                                        |
+-----------------------------+----------------------------------------------------------------------------+
| CO_STORAGE_NAME             | DB storage database name (default: "coordinator")                          |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_HOST         | Client chain rpc host address                                              |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_USER         | Client chain rpc username                                                  |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_PASS         | Client chain rpc password                                                  |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_GENESIS_HASH | Client chain genesis hash                                                  |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_BLOCK_TIME   | Client chain block find time interval in seconds (default: 60)             |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_ASSET_KEY    | client chain challenge asset key                                           |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_ASSET        | client chain challenge asset/assetlabel (default: "CHALLENGE")             |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_CHAIN        | client chain chain name                                                    |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_PAYMENT_ASSET| client chain fee payment asset                                             |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_PAYMENT_KEY  | client chain fee payment key (optional; for payment mode only)             |
+-----------------------------+----------------------------------------------------------------------------+
| CO_CLIENTCHAIN_PAYMENT_ADDR | client chain address for payment funds (optional; for payment mode only)   |
+-----------------------------+----------------------------------------------------------------------------+
| CO_SERVICE_HOST             | service chain host address                                                 |
+-----------------------------+----------------------------------------------------------------------------+
| CO_SERVICE_USER             | service chain username                                                     |
+-----------------------------+----------------------------------------------------------------------------+
| CO_SERVICE_PASS             | service chain passord                                                      |
+-----------------------------+----------------------------------------------------------------------------+



Demo
----
A demo is included to test the coordinator locally. The ``demo.sh`` will spin up a fresh chain and create a request along with 2 bids. The alias commands at the beginning of the script must be directed to your `ocean/src/` directory. Using this demo chain the coordinator demo creates two guardnodes and challenges them for information about the client chain. When they respond the coordinator output shows them being verified and stored ready for fee payments to be made later.

To run the demo:

.. code-block:: bash

  ./scripts/demo.sh && cargo run --example demo

This script is also used in a `tutorial <https://commerceblock.readthedocs.io/en/latest/guardnode/index.html#demo>`_ showing guardnode functionality and interaction with Coordinator.

After the script is complete the coordinator demo creates two guardnodes and randomly challenges them by randomly including CHALLENGE asset transactions in blocks. When the guardnoes respond the coordinator output shows them being verified and stored ready for fee payments to be made later.

RPC API
-------

Any requests need to be sent to ``CO_API_HOST`` using Http Basic Authentication via ``CO_API_USER``/``CO_API_PASS``.

The following rpc commands are offered:

* ``getrequests {"page": <int, optional>}``: fetches up to 10 requests for the client. more can be requested with the "page" parameter
* ``getrequest {"txid": "hash"}``: fetches the specific request
* ``getrequestreponse {"txid": "hash"}``: fetches the responses for a specific request

An example of how to generate a response report is showing in `report <https://github.com/commerceblock/coordinator/blob/develop/scripts/report.py>`_.

Example

.. code-block:: bash

    curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method": "getrequestresponse", "params" :     {"txid": "5eba0bf305ac8963225d68195fa7eb8b79667ad9c5fa6e9dcc0ce0185ad4a046"}, "id":1 }' userApi:passwordApi@localhost:3333


Guarnode Responses
------------------

Guardnode responses are sent via HTTP POST to the listener host at:

`http://coordinator:8080/challengeproof`.

The fields "txid" (challenge transaction txid), "pubkey" (guardnode public key), "hash" (guardnode bid txid) and "sig" (signed txid with guardnode pubkey) need to be included in the body of the POST request.

An example of this is shown in `hyperclient <https://github.com/commerceblock/coordinator/blob/develop/examples/hyperclient.rs>`_.

Example

Request

.. code-block:: bash

  { method: POST, uri: http://localhost:9999/challengeproof, version: HTTP/1.1, headers: {"content-type": "application/json"}, body: Body }


Body

.. code-block:: bash

  {\"txid\":\"1234567890000000000000000000000000000000000000000000000000000000\",\"pubkey\":\"026a04ab98d9e4774ad806e302dddeb63bea16b5cb5f223ee77478e861bb583eb3\",\"hash\":\"0404040404040404040404040404040404040404040404040404040404040404\",\"sig\":\"30450221009dd76bcdc19a283654727214757b9e33ded38f00951b4f4a074e6fbe17a6f2ef02205702423facf6333cfce1e17d5427f98b073ebf8b587dad1a1d44696d44c26e6b\"}
