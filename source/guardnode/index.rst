Guardnode daemon
============

Guardnode daemon responding to client chain coordinator challenges and generating alerts for misbehavior on the chain.

Running
-------

To run the daemon locally:

Install and build

.. code-block:: bash

  pip3 install -r requirements.txt
  python3 setup.py build && python3 setup.py install

Run either

.. code-block:: bash

  ./run_guardnode ...

Or

.. code-block:: bash

  python3 -m guardnode ...

providing the arguments required.


Demo
----
To run a demo along with the `coordinator <https://github.com/commerceblock/coordinator>`_ daemon execute the following replacing ``$txid`` with the txid produced by the coordinator `demo script <https://github.com/commerceblock/coordinator/scripts/demo.sh>`_:

.. code-block:: bash

  ./run_guardnode --rpcuser user1 --rpcpassword password1 --bidpubkey 029aaa76fcf7b8012041c6b4375ad476408344d842000087aa93c5a33f65d50d92 --challengeasset fae9f771019d45e31b8f78da99a15b094b17b2ba76b0940c3ac53d5e9afd8e8e --nodelogfile /Users/nikolaos/co-client-dir/ocean_test/debug.log --bidtxid $txid


Configuration
-------------

Arguments to set:

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
| --bidtxid        | Guardnode winning bid txid       |
+------------------+----------------------------------+
| --bidpubkey      | Guardnode winning bid public key |
+------------------+----------------------------------+
| --challengehost  | Challenge host address           |
+------------------+----------------------------------+
| --challengeasset | Challenge asset hash             |
+------------------+----------------------------------+


Running services with docker-compose
====================================

Clone data directories

.. code-block:: bash

  git clone https://github.com/commerceblock/guardnode.git && cd guardnode


Start ocean node:

.. code-block:: bash

  docker-compose -p ocean -f contrib/docker-compose/cb-guardnode-testnet.yml up -d ocean

Start guardnode:

.. code-block:: bash

  docker-compose -p ocean -f contrib/docker-compose/cb-guardnode-testnet.yml up -d guardnode

Check status:

.. code-block:: bash

  docker-compose -p ocean -f contrib/docker-compose/cb-guardnode-testnet.yml ps

Check ocean logs:

.. code-block:: bash

  docker-compose -p ocean -f contrib/docker-compose/cb-guardnode-testnet.yml logs --follow ocean

Check guarnode logs:

.. code-block:: bash

  docker-compose -p ocean -f contrib/docker-compose/cb-guardnode-testnet.yml logs --follow guardnode
