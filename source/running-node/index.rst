Ocean Node Setup 
******************

To run an Ocean node for a specific sidechain, the following is required:

* Download/install the Ocean client binaries.

* Configure the node using a configuration file and contract.

The applications required:

**Ocean Daemon (oceand)**

An Ocean node that runs as a background application. It can processes requests made from
other applications using Remote Procedure Calls (RPC). 

**Ocean Client (ocean-cli)**

The client application that enables command line calls to ``oceand`` by issuing
RPC commands. Commands for ``ocean-cli`` are specified in the Ocean API Reference.  

Installation
------------

Ocean can be downloaded from the `CommerceBlock Github repository <https://github.com/commerceblock/ocean/releases>`_ .

Alternatively, executables can be compiled directly from the `source code <https://github.com/commerceblock/ocean/releases>`_. 

After installation, the node must then be configured. 

Configuration
-------------

The ``oceand`` and ``ocean-cli`` applications use a configuration file
named ``ocean.conf``. This file defines the consensus rules of the sidechain, specify which network
to connect to and can set a number of different behaviors within the application. It also
defines what credentials must be provided in order to use the RPC interface. The
``ocean-cli`` application uses the configuration file to obtain the correct credentials in
order to communicate with ``oceand`` using RPC. 

When either of these applications are started you must provide a ``datadir`` path.
The path you provide tells the applications which directory to use to:

* Obtain RPC authentication data (user, password, port).

* Store blockchain and wallet data.

* Store log files etc.

Download the sidechain specific ``ocean.conf`` file from the sidechain operator or asset issuer, and copy it to the 
``datadir`` path. If there is a contract for the sidechain (terms and conditions) this will need to be 
copied to a ``terms-and-conditions`` directory in the ``datadir``. 

Running the Ocean node
------------------------

Once the configuration file and contract are in place the Ocean daemon can be started.

**Linux**

Run each application from the command line within the folder
you extracted them to, along with the ``-datadir`` argument. For example:

.. code-block:: bash

  ./oceand -datadir=path

and 

.. code-block:: bash

  ./ocean-cli -datadir=path

Depending on your system set up, you may have to change the permissions on the files before they will run.

RPC commands can then be passed to the client via ``ocean-cli``

To verify the genesis block hash created with the configuration, run:

.. code-block:: bash

  ./ocean-cli -datadir=path getblockhash 0

To query the synchronization status of the node, run:

.. code-block:: bash

  ./ocean-cli -datadir=path getblockchaininfo

Launching with Docker
---------------------

Instructions for launching a full configured Ocean node with Docker. 

Requirements
"""""""""""""""""""""""""""

Docker engine release: 18.02.0 or latest

docker-compose: 1.20.0 or latest

Download docker-compose.yml
"""""""""""""""""""""""""""

Download the ``docker-compose.yml`` file and the contract (terms and conditions) from the sidechain operator or asset issuer. 

For example (Ocean testnet):

.. code-block:: console

   curl -O https://raw.githubusercontent.com/commerceblock/ocean/master/contrib/docker/docker-compose.yml


.. code-block:: console

   curl -O https://raw.githubusercontent.com/commerceblock/ocean/master/doc/terms-and-conditions/ocean_test/latest.txt

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


Check logs 
"""""""""""""""""""""""""""""""""""""

.. code-block:: console

   docker-compose -p ocean logs --follow

Hit ctrl+c to stop following

Check if connection
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
