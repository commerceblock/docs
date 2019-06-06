Running with Docker
==========================

Instructions for launching a full Ocean node with Docker. 

Requirements
------------

Docker engine release: 18.02.0 or latest

docker-compose: 1.20.0 or latest

Download docker-compose.yml
"""""""""""""""""""""""""""
From:
 commerceblock/ocean/contrib/docker/docker-compose.yml

Or

.. code-block:: console

   curl -O https://raw.githubusercontent.com/commerceblock/ocean/master/contrib/docker/docker-compose.yml

Download and read terms and conditions
""""""""""""""""""""""""""""""""""""""

.. code-block:: console
   curl -O https://raw.githubusercontent.com/commerceblock/ocean/master/doc/terms-and-conditions/ocean_main/latest.txt

Download image and start
""""""""""""""""""""""""

.. code-block:: console

   docker-compose -p ocean up -d

Check status
""""""""""""

.. code-block:: console

   docker-compose -p ocean ps

Output
""""""

.. code-block:: console

       Name                  Command               State                         Ports
   ---------------------------------------------------------------------------------------------------------
   ocean_node_1   /docker-entrypoint.sh elem ...   Up      0.0.0.0:32768->18332/tcp, 0.0.0.0:32769->7042/tcp


Check logs and see if node is syncing
"""""""""""""""""""""""""""""""""""""

.. code-block:: console

   docker-compose -p ocean logs --follow

Hit ctrl+c to stop following

Check if connected to CommerceBlock testnet
"""""""""""""""""""""""""""""""""""""""""""

.. code-block:: console

 docker-compose -p ocean exec node ocean-cli -rpcport=18332 -rpcuser=ocean -rpcpassword=oceanpass getpeerinfo

 Should see: "testnet.commerceblock.com:7043"

Check block count
"""""""""""""""""

.. code-block:: console

   docker-compose -p ocean exec node ocean-cli -rpcport=18332 -rpcuser=ocean -rpcpassword=oceanpass getblockcount

Once synced, block count should be the same as in: https://cbtexplorer.com

Data persistence
""""""""""""""""

.. code-block:: console

   mkdir ~/ocean_full_node
   mkdir -p ~/ocean_full_node/terms-and-conditions/ocean_test
   cp latest.txt ~/ocean_full_node/terms-and-conditions/ocean_test/
   
   edit: docker-compose.yml, adding:

    image: commerceblock/ocean:latest
    volumes:
      - /home/your_username/ocean_full_node:/home/bitcoin/.bitcoin

Using docker secrets
""""""""""""""""""""

Add a secrets block to contrib/docker/docker-compose.yml

.. code-block:: console

   secrets:
     ocean_user:
       file: ocean_user
     ocean_pass:
       file: ocean_pass


Modify the service block to use the newly defined secrets:

.. code-block:: console

   services:
     node:
       secrets:
         - ocean_user
         - ocean_pass

Remove the rpc authentication arguments from the command:

.. code-block:: console

   -rpcuser=${BITCOIN_RPC_USER:-ocean}
   -rpcpassword=${BITCOIN_RPC_PASSWORD:-oceanpass}

Create and populate ocean_user and ocean_pass files with credentials in the same directory.

Dig deeper
""""""""""

As root

.. code-block:: console

   docker-compose -p ocean exec node bash`

As bitcoin

.. code-block:: console

   docker-compose -p ocean exec -u bitcoin node bash


Then: ocean-cli / ocean-tx available from within inside of container.

Note: if running as root, need to specify: -datadir=/home/bitcoin/.bitcoin

Execute shell commands
""""""""""""""""""""""

.. code-block:: console

   docker-compose -p ocean exec node ip a

Scale containers
""""""""""""""""

Up

.. code-block:: console

  docker-compose -p ocean scale node=2

Down

.. code-block:: console

   docker-compose -p ocean scale node=1

Stop
""""

.. code-block:: console

   docker-compose -p ocean stop

Remove stack
""""""""""""

.. code-block:: console

   docker-compose -p ocean rm -f
