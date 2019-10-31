ERC20-Sidechain bridge
=======================

A short guide on how to peg-in CBT tokens from the Ethereum network to Ocean and peg-out back to Ethereum.

The following steps assume that the user is running a full Ocean node connected to the CommerceBlock mainnet. The node needs to be synced up and with RPC connectivity enabled. Connectivity to a geth node is optional but it would allow doing the same validation checks that a signing node does for the peg-in transaction.

**Step 1**

Export the private key of the ethereum address that owns the CBT tokens. This should be in hex format, e.g.
::

    "0xcb850d9db23b54ebbeae09995f7192af83646f9ea232645bb5a71699e5c15a6e"

**Step 2**

Run the ``getethpeginaddress`` RPC using this private key (with the "0x" prefix removed):

.. code-block:: bash

    ocean-cli getethpeginaddress cb850d9db23b54ebbeae09995f7192af83646f9ea232645bb5a71699e5c15a6e
    {
      "eth_mainchain_address": "b6872561de5ba19d38071a7616d9d434b9e37860",
      "eth_claim_pubkey": "03664b8a3e065329c6bb3b8f9f0bb382179775f609ffa9ff564ea6f20e913ec04b"
    }


**Step 3**

Pay the CBT tokens to the ``eth_mainchain_address`` returned from the ``getethpeginaddress`` RPC and save the transaction id. The transaction will require a minimum amount of 10 confirmations before being allowed to peg-in.


**Step 4**

Run the ``claimethpegin`` RPC using the ``eth_claim_pubkey`` returned above, the transaction id and the CBT amount as:

.. code-block:: bash

    ocean-cli claimethpegin $txid $amount 03664b8a3e065329c6bb3b8f9f0bb382179775f609ffa9ff564ea6f20e913ec04b

**Step 5**

Run the ``getbalance`` RPC and verify that the CBT has been pegged in to the Ocean network.

**Step 6**

To peg-out this CBT will require running the ``sendtoethmainchain`` RPC specifying an address to send the CBT to as well as the amount to peg-out:

.. code-block:: bash

    ocean-cli sendtoethmainchain 8e8a0ec05cc3c2b8511aabadeeb821df19ea7564 0.1
