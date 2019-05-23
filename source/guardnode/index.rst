Guardnode daemon
============

Guardnode daemon responding to client chain coordinator challenges and generating alerts for misbehavior on the chain.

Requirements
------------

* Run a client chain Ocean full node
* Generate an address and extract the pubkey
* Use the Guardnode wallet to bid on active requests for the clientchain
* Run the Guardnode daemon connecting to the client chain node
* Monitor the client chain and send challenge responses to the coordinator
* Receive client chain assets on the address at the end of the request period

Running
-------

To run the daemon locally:

1. ``pip3 install -r requirements.txt``
2. ``python3 setup.py build && python3 setup.py install``
3. Run ``./run_guardnode`` or ``python3 -m guardnode`` providing the arguments required


To run a demo along with the `coordinator <https://github.com/commerceblock/coordinator>`_ daemon execute the following replacing `$txid` with the txid produced by the coordinator `demo script <https://github.com/commerceblock/coordinator/scripts/demo.sh>`_:

``./run_guardnode --rpcuser user1 --rpcpassword password1 --pubkey 029aaa76fcf7b8012041c6b4375ad476408344d842000087aa93c5a33f65d50d92 --challengeasset 73be00507b15f79efccd0184b7ca8367372dfd5334ae8991a492f5f354073c88 --bidtxid $txid``

Configuration
-------------

Arguments to set:

* ``--rpcconnect``: Client RPC host
* ``--rpcport``: Client RPC port
* ``--rpcuser``: Client RPC username
* ``--rpcpassword``: Client RPC password
* ``--bidtxid``: Guardnode winning bid txid
* ``--pubkey``: Guardnode public key
* ``--coordinator``: Coordinator host address
* ``--challengeasset``: Challenge asset hash
* ``--addressprefix``: Chain P2PKH address prefix
