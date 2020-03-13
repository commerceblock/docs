Guardnode daemon
============

Guardnode daemon responding to client chain coordinator challenges and generating alerts for misbehavior on the chain.

Running
-------

Find instructions for running the guardnode daemon in the `Guardnode repo <https://github.com/commerceblock/guardnode/>`_.

Viewing and Spending locked outputs
-----------------------

When a successful bid is made your guardnode will lock up your CBT for the duration of the service period. These outputs are not recognised by some RPCs such as ``getBalance``. To include your locked funds in a call to ``getBalance`` include all addresses and watch only addresses with the following arguments:

.. code-block:: bash

  getbalance "*" 0 true


These funds can be respent but only using RPCs that allow watch only addresses in their coin selection, therefore RPCs such as ``sendanytoaddress`` will not recognise the previously locked funds. Using ``createanytoaddress`` with the ``allowwatchonly`` argument will include previously locked outputs in its inputs, e.g:

.. code-block:: bash

  createanytoaddress "$ADDR" $AMOUNT true true 1 true

Then sign and send the transaction using ``signrawtransaction`` and ``sendrawtransaction``.

Demo
----
The following is a demo of the Guardnode in action responding to a challenge. First run the `demo script <https://github.com/commerceblock/coordinator/blob/master/scripts/demo.sh>`_ which generates a request and two bids for that request on a mock service chain.

Next, in a separate terminal window execute the following script and watch the guardnode recognise the request and submit a bid.

.. code-block:: bash

  ./run_guardnode --rpcuser user1 --rpcpass password1 --rpchost 127.0.0.1:5555 --servicerpcuser user1 --servicerpcpass password1 --servicerpchost 127.0.0.1:5555 --nodelogfile $HOME/co-client-dir/ocean_test/debug.log --challengehost 127.0.0.1:5555 --bidlimit 50 --serviceblocktime 5

We can now mine some blocks to bring our request into its service period. Then we can send a CHALLENGE asset transaction and watch the guardnode react. In the first terminal window execute:

.. code-block:: bash

  alias ocn='/$HOME/ocean/src/ocean-cli -datadir=$HOME/co-client-dir'
  ocn generate 5
  ocn sendtoaddress $(ocn getnewaddress) 1 "" "" false "CHALLENGE"
  ocn generate 1

As there is no connection to a coordinator we get an error message but the would-be response message is displayed. Guardnode sends their bid txid to identify themselves, the challenge tx hash and a signature to coordinator as a response to the challenge and thus prove their active watching of the client chain. The coordinator uses this to determine whether to send payment to each guardnode at the end of the service period depending on whether they responded correctly to each challenge or not.

Further guardnode functionality such as invalid block and consensus anomaly detection will be implemented soon.

Demo with coordinator
---------------------

We can repeat the same demo with connection to a coordinator and observe the process of coordinator generating challenges, guardnodes sending responses and coordinator verifying them.

Run a `coordinator <https://github.com/commerceblock/coordinator>`_ daemon and execute the following:

.. code-block:: bash

  ./run_guardnode --rpcuser user1 --rpcpass password1 --rpchost 127.0.0.1:5555 --servicerpcuser user1 --servicerpcpass password1 --servicerpchost 127.0.0.1:5555 --nodelogfile $HOME/co-client-dir/ocean_test/debug.log --challengehost 127.0.0.1:5555 --bidlimit 50 --serviceblocktime 5


Configuration
-------------

The full list of arguments are given below:

+--------------------+---------------------------------------------------------+
| Argument           |  Decription                                             | 
+====================+=========================================================+
| --rpchost          | Client chain RPC host address                           |
+--------------------+---------------------------------------------------------+
| --rpcuser          | Client chain RPC username                               |
+--------------------+---------------------------------------------------------+
| --rpcpass          | Client chain RPC password                               |
+--------------------+---------------------------------------------------------+
| --servicerpchost   | Service chain RPC host address                          |
+--------------------+---------------------------------------------------------+
| --servicerpcuser   | Service chain RPC username                              |
+--------------------+---------------------------------------------------------+
| --servicerpcpass   | Service chain RPC password                              |
+--------------------+---------------------------------------------------------+
| --serviceblocktime | Service chain block time                                |
+--------------------+---------------------------------------------------------+
| --nodelogfile      | Client chain log file destination                       |
+--------------------+---------------------------------------------------------+
| --bidpubkey        | (Optional) Pre-made bid public key                      |
+--------------------+---------------------------------------------------------+
| --bidlimit         | Upper limit in bid size                                 |
+--------------------+---------------------------------------------------------+
| --challengehost    | Challenger host address                                 |
+--------------------+---------------------------------------------------------+
| --uniquebidpubkeys | Flag to indicate new bid pubkey generation for each bid |
+--------------------+---------------------------------------------------------+


