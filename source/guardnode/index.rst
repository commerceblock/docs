Guardnode daemon
============

Guardnode daemon responding to client chain coordinator challenges and generating alerts for misbehavior on the chain.

Running
-------

To run the daemon locally:

1. ``pip3 install -r requirements.txt``
2. ``python3 setup.py build && python3 setup.py install``
3. Run ``./run_guardnode`` or ``python3 -m guardnode`` providing the arguments required


To run a demo along with the `coordinator <https://github.com/commerceblock/coordinator>`_ daemon execute the following replacing `$txid` with the txid produced by the coordinator `demo script <https://github.com/commerceblock/coordinator/scripts/demo.sh>`_:

``./run_guardnode --rpcuser user1 --rpcpassword password1 --bidpubkey 029aaa76fcf7b8012041c6b4375ad476408344d842000087aa93c5a33f65d50d92 --challengeasset fae9f771019d45e31b8f78da99a15b094b17b2ba76b0940c3ac53d5e9afd8e8e --nodelogfile /Users/nikolaos/co-client-dir/ocean_test/debug.log --bidtxid $txid``

Configuration
-------------

Arguments to set:

* `--rpcconnect`: Client RPC host
* `--rpcport`: Client RPC port
* `--rpcuser`: Client RPC username
* `--rpcpassword`: Client RPC password
* `--nodeaddrprefix`: Node P2PKH address prefix
* `--nodelogfile`: Node log file destination
* `--bidtxid`: Guardnode winning bid txid
* `--bidpubkey`: Guardnode winning bid public key
* `--challengehost`: Challenge host address
* `--challengeasset`: Challenge asset hash
