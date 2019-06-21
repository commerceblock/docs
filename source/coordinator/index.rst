Coordinator daemon
============

Coordinator daemon, responsible for verifying the operation of Guardnodes in the Commerceblock Covalence system


Requirements
------------

* CB service chain connectivity to receive active client requests
* Client chain connectivity to generate guardnode challenges
* Db instance to store requests and responses
* Listener HTTP POST endpoint to receive guardnode responses
* Public rpc api to offer information on requests and guardnode response performance


Configuration
-------------

Env variables to set:

* CO_LISTENER_HOST: host address at which the coordinator binds to receive guardnode responses
* CO_CHALLENGE_FREQUENCY: the frequency in number of blocks that new challenges are created
* CO_CHALLENGE_DURATION: challenge duration in seconds
* CO_VERIFY_DURATION: challenge transaction verify duration in seconds
* CO_LOG_LEVEL: env logger log level
* CO_API_HOST: rpc api host address
* CO_API_USER: rpc api user name
* CO_API_PASS: rpc api password
* CO_STORAGE_HOST: db storage host address
* CO_STORAGE_USER: db storage user name
* CO_STORAGE_PASS: db storage password
* CO_STORAGE_NAME: db storage database name
* CO_CLIENTCHAIN_HOST: client chain rpc host address
* CO_CLIENTCHAIN_USER: client chain rpc user name
* CO_CLIENTCHAIN_PASS: client chain rpc password
* CO_CLIENTCHAIN_ASSET_HASH: client chain challenge asset hash
* CO_CLIENTCHAIN_GENESIS_HASH: client chain genesis hash
* CO_CLIENTCHAIN_ASSET: client chain challenge asset label
* CO_CLIENTCHAIN_ASSET_KEY: client chain challenge asset key
* CO_SERVICE_HOST: service chain host address
* CO_SERVICE_USER: service chain user name
* CO_SERVICE_PASS: service chain password


Running
-------

To run a production instance of the coordinator along with a mongo db database, edit the envs in the `docker compose file <https://github.com/commerceblock/coordinator/blob/develop/docker-compose.yml>`_ file and:

`docker-compose up`

To test the coordinator locally:

`cargo run`

To test the coordinator locally with demo request creation:

`./scripts/demo.sh && cargo run --example demo`


RPC API
-------

Any requests need to be sent to CO_API_HOST using Http Basic Authentication via CO_API_USER/CO_API_PASS.

The following rpc commands are offered:

* getrequests: fetches all requests for the client
* getrequest {"txid": "hash"}: fetches the specific request
* getrequestreponses {"txid": "hash"}: fetches the responses for a specific request

An example of how to generate a response report is showing in `report <https://github.com/commerceblock/coordinator/blob/develop/scripts/report.py>`_.

Example
```
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0", "method": "get_challenge_responses", "params" : {"txid": "5eba0bf305ac8963225d68195fa7eb8b79667ad9c5fa6e9dcc0ce0185ad4a046"}, "id":1 }' userApi:passwordApi@localhost:3333
```

Guarnode Responses
------------------

Guardnode responses are sent via HTTP POST to the listener host at:

`http://coordinator:8080/challengeproof`.

The fields "txid" (challenge transaction txid), "pubkey" (guardnode public key), "hash" (guardnode bid txid) and "sig" (signed txid with guardnode pubkey) need to be included in the body of the POST request.

An example of this is shown in `hyperclient <https://github.com/commerceblock/coordinator/blob/develop/examples/hyperclient.rs>`_.

Example
Request
```
{ method: POST, uri: http://localhost:9999/challengeproof, version: HTTP/1.1, headers: {"content-type": "application/json"}, body: Body }
```

Body
```
{\"txid\":\"1234567890000000000000000000000000000000000000000000000000000000\",\"pubkey\":\"026a04ab98d9e4774ad806e302dddeb63bea16b5cb5f223ee77478e861bb583eb3\",\"hash\":\"0404040404040404040404040404040404040404040404040404040404040404\",\"sig\":\"30450221009dd76bcdc19a283654727214757b9e33ded38f00951b4f4a074e6fbe17a6f2ef02205702423facf6333cfce1e17d5427f98b073ebf8b587dad1a1d44696d44c26e6b\"}
```
