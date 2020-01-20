Guardnode daemon
============

Guardnode daemon responding to client chain coordinator challenges and generating alerts for misbehavior on the chain.

Running
-------

Find instructions for running the guardnode daemon in the `Guardnode repo <https://github.com/commerceblock/guardnode/>`_.


Demo
----
The following is a demo of the Guardnode in action responding to a challenge. First run the `demo script <https://github.com/commerceblock/coordinator/blob/master/scripts/demo.sh>`_ which generates a request and two bids for that request on a mock service chain.

Next, in a separate terminal window, execute the following replacing ``$txid`` with the ``Guardnode txid`` produced by the demo script.

.. code-block:: bash

  ./run_guardnode --rpcuser user1 --rpcpassword password1 --bidpubkey 029aaa76fcf7b8012041c6b4375ad476408344d842000087aa93c5a33f65d50d92 --nodelogfile $HOME/co-client-dir/ocean_test/debug.log --challengehost http://127.0.0.1:9999 --bidtxid $txid

We can now send a CHALLENGE asset transaction and watch the guardnode react. In the first terminal window execute:

.. code-block:: bash

  alias ocn='/$HOME/ocean/src/ocean-cli -datadir=$HOME/co-client-dir'
  ocn sendtoaddress $(ocn getnewaddress) 1 "" "" false "CHALLENGE"
  ocn generate 1

As there is no connection to a coordinator we get an error message but the would-be response message is displayed. Guardnode sends their bid txid to identify themselves, the challenge tx hash and a signature to coordinator as a response to the challenge and thus prove their active watching of the client chain. The coordinator uses this to determine whether to send payment to each guardnode at the end of the service period depending on whether they responded correctly to each challenge or not.

Further guardnode functionality such as invalid block and consensus anomaly detection will be implemented soon.

Demo with coordinator
---------------------

We can repeat the same demo with connection to a coordinator and observe the process of coordinator generating challenges, guardnodes sending responses and coordinator verifying them.

Run a `coordinator <https://github.com/commerceblock/coordinator>`_ daemon and execute the following replacing ``$txid`` with the txid produced by the coordinator `demo script <https://github.com/commerceblock/coordinator/blob/master/scripts/demo.sh>`_:

.. code-block:: bash

  ./run_guardnode --rpcuser user1 --rpcpassword password1 --bidpubkey 029aaa76fcf7b8012041c6b4375ad476408344d842000087aa93c5a33f65d50d92 --nodelogfile $HOME/co-client-dir/ocean_test/debug.log --bidtxid $txid


Configuration
-------------

The full list of arguments are given below:

+------------------+----------------------------------+
| Argument         |  Decription                      |
+==================+==================================+
| --rpcconnect     | Client RPC host                  |
+------------------+----------------------------------+
| --rpcuser        | Client RPC username              |
+------------------+----------------------------------+
| --rpcport        | Client RPC port                  |
+------------------+----------------------------------+
| --rpcpassword    | Client RPC password              |
+------------------+----------------------------------+
| --nodeaddrprefix | Node P2PKH address prefix        |
+------------------+----------------------------------+
| --nodelogfile    | Node log file destination        |
+------------------+----------------------------------+
| --bidpubkey      | Guardnode winning bid public key |
+------------------+----------------------------------+
| --challengehost  | Challenge host address           |
+------------------+----------------------------------+


