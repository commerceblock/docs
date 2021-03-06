Guardnode guide
===============

A step by step guide for setting up the Guardnode stack that includes a CommerceBlock service chain full node, a client chain full node and guardnode daemon guarding the client chain.

In order to run the guardnode service navigate to the config directory of the corresponding client shown below, download the docker-compose file and follow the README instructions on how to run the node using data persistence. The current active client services are:

- DGLD mainnet chain `config <https://github.com/goldtokensa/config/tree/master/mainnet>`_
- CB testnet chain `config <https://github.com/commerceblock/config>`_

The service node wallet will need to be funded with CBT in order to provide services. This can be done by paying to an address generated by the node or import a private key from another wallet. For more info on this check `erc20 peg <https://commerceblock.readthedocs.io/en/latest/twowp/index.html>`_

To start the service chain node, client chain node and guardnode run:

.. code-block:: bash

    docker-compose up -d

The guardnode will automatically look for requests in the specific client chain and bid for them at the current auction price. This will only work if the service chain node wallet has been funded with CBT. Guardnode owners can limit the amount of CBT they are willing to bid with by overriding the `--bidlimit` option in the guardnode container in the compose file downloaded above.

Guardnode operators should monitor the logs and alert CB about any issues in the client chain by:

.. code-block:: bash

    docker-compose logs -f guardnode
