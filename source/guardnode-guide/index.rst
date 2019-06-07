Guardnode Guide
============

A step by step guide on how to setup a service chain node, client chain node and guardnode daemon.

1. Run the Ocean chain service node

Download the docker-compose file from https://github.com/commerceblock/ocean/tree/master/contrib/docker and follow the instructions under _how to_.

Use the command line interface to find about active requests:

.. code-block:: bash

    ocean-cli getrequests

2. Run the client chain node

By picking one of the requests returned decide which client chain to offer the services from and download the composed docker-compose file from https://github.com/commerceblock/guardnode/tree/master/contrib/docker-compose.

Start the client ocean node:

.. code-block:: bash

    docker-compose start ocean

Using the client chain node generate a pubkey on which the fee rewards will be paid by:

.. code-block:: bash

    addr=`ocean-cli getnewaddress`
    pub=`ocean-cli validateaddress $addr | jq -r ".pubkey"`


3. Create a bid transaction

The following script can be used to generate a bid for an active request. Several parameters will need to be filled specific to the request.

.. code-block:: bash

    #!/bin/bash
    shopt -s expand_aliases

    alias ocl="ocean-cli -rpcport=7043 -rpcuser=ocean -rpcpassword=oceanpass"

    echo "Creating bid in service chain"

    # Address tokens will be locked in
    addr=`ocl getnewaddress`
    pub=`ocl validateaddress $addr | jq -r ".pubkey"`

    # Get asset unspent
    unspent=`ocl listunspent 1 9999999 [] true "CBT" | jq .[0]`
    asset_hash=`echo $unspent | jq -r ".asset"`
    value=`echo $unspent | jq -r ".amount"`
    txid=`echo $unspent | jq ".txid"`
    vout=`echo $unspent | jq -r ".vout"`

    # TO UNLOCK A PREVIOUS BID
    # Provide the `txid` and `vout` for that transaction
    # The output can be spent after the locktime is expired

    # Fee
    fee=0.001
    # Current auction price
    bid=
    # Change from unspent
    change=$(echo "$value - $fee - $bid" | bc)

    # Request id in service chain
    requestid=""
    # Request end height
    end=
    # Fee pubkey to pay fees in clientchain
    feepub=""

    inputs="[{\"txid\":$txid,\"vout\":$vout,\"asset\":\"$asset_hash\"}]"
    outputs="{\"endBlockHeight\":$end,\"requestTxid\":\"$requestid\",\"pubkey\":\"$pub\",\
    \"feePubkey\":\"$feepub\",\"value\":$bid,\"change\":$change,\"changeAddress\":\"$addr\",\"fee\":$fee}"

    signedtx=`ocl signrawtransaction $(ocl createrawbidtx $inputs $outputs)`
    txidbid=`ocl sendrawtransaction $(echo $signedtx | jq -r ".hex")`
    echo "txid: $txidbid"

4. Run the guardnode service

Using the ocean service node verify that your bid has been approved using:
`ocean-cli getrequestbids $requesttxid`

Once verified fill the `bidpubkey` and `bidpubkey` on the docker compose file downloaded for the client chain and start the guardnode service by:

.. code-block:: bash

    docker-compose start guardnode

Monitor the logs using:

.. code-block:: bash

    docker-compose logs --follow guardnode
