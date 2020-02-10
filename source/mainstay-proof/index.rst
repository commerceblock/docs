Mainstay service client
-----------------------

The Mainstay service client is a pure Python command-line tool used to manage interaction with the **mainstay.xyz** API and proof
service. The tool can be used to perform state attestations, retrieve and collate 
proofs and to verify proofs of immutable sequence via connection to a Bitcoin 
full node or Bitcoin block-explorer API. 

Requirements
=============

Python3

To verify proofs against the Bitcoin blockchain, RPC or HTTP connection details to a `bitcoind` node or trusted block explorer must be provided. 

Installation
=============

Via PyPi:

.. code-block:: bash

  pip3 install pymainstay

or directly from source (downloaded from `Github <https://github.com/commerceblock/pymainstay>`_):

.. code-block:: bash

  python3 setup.py install

Usage
======

The Mainstay client interface (`msc`) can be used to fetch and verify proof sequences, synchronize and verify the immutability and uniqueness of sidechains and state sequences, perform authenticated data commitments and attestations, and generate and manage *mainstay.xyz* authentication keys. The interface is used via commands to perform different operations with specified arguments. The commands available can be listed with the ``--help`` argument:

.. code-block:: bash

  usage: msc [-h] [-q] [-v]
              {attest,a,fetch,f,verify,v,sync,s,config,c,keygen,k,info,i} ...

  Mainstay client

  optional arguments:
    -h, --help            show this help message and exit
    -q, --quiet           Be more quiet.
    -v, --verbose         Be more verbose. Both -v and -q may be used multiple
                          times.

  Commands:
    Mainstay operations are performed via commands:

    {attest,a,fetch,f,verify,v,sync,s,config,c,keygen,k,info,i}
      attest (a)          Commit data to a Mainstay slot
      fetch (f)           Fetch proofs from the Mainstay service
      verify (v)          Verify Mainstay proofs against the Bitcoin blockchain
      sync (s)            Syncronise sidechain to Bitcoin via a sequence proof
      config (c)          Set configuration
      keygen (k)          Generate signing keys for attestations
      info (i)            Mainstay service status information 


For each command, the possible arguments can be again listed with the `--help` flag. E.g. 

.. code-block:: bash

  msc attest -h

.. code-block:: bash

  usage: msc attest [-h] [-f FILENAME | -c COMMITMENT | -g GIT | -d DIRECTORY]
                    [-s SLOT] [--url SERVICE_URL] [-t API_TOKEN] [-k PRIVKEY]

  optional arguments:
    -h, --help            show this help message and exit
    -f FILENAME, --file FILENAME
                          Attest the SHA256 hash of the specified file.
    -c COMMITMENT, --commit COMMITMENT
                          Hex string of the 32 bytes commitment.
    -g GIT, --git GIT     Attest the HEAD of the specified Git repository. If 0
                          use stored path.
    -d DIRECTORY, --dir DIRECTORY
                          Attest the state of the specified sequence directory.
    -s SLOT, --slot SLOT  Specify the slot position index.
    --url SERVICE_URL     URL for the Mainstay connector service. Default:
                          https://mainstay.xyz
    -t API_TOKEN, --token API_TOKEN
                          API token for the specified slot position.
    -k PRIVKEY, --privkey PRIVKEY
                          Private key for signing the commitment.


Configuration
^^^^^^^^^^^^^^

The client can be used in a stateless fashion, with all configuration supplied via the command-line options, however a configuration file (``config.json``) can be used, which is located in the application data directory. The current configuration, and the location of the application data directory on a particular system, can be retrieved as follows:

``msc config``

All configuration is set via the same command. For connection to a particular slot, the slot position (``-s``), the API token (``-t``) and the authentication key (``-k``) can be set initially, so they do not have to be specified as arguments for each subsequent call. 

Attestation
^^^^^^^^^^^^

To perform commitments to a specified *mainstay.xyz* slot requires an API token that will have been provided on initialization of the slot. In addition, if a public key was specified on initialization, the commitment must be signed by the corresponding private key. The signature is computed by the client if the private key is provided (or is set in the config). 

The client will send 32 byte commitment supplied as an argument (``-c``) to the specified slot, or the ``SHA256`` hash of a specified file path (``-f``). For example:

.. code-block:: bash

  msc attest -c 4db3dbb10b33d94389446982f022ee55be8eaefa7d8f40046054a693f23a1c85 -s 2

The client will return whether the commitment has been recieved by the *mainstay.xyz* successfully. 

To attest the state of a file sequence directory, supply the directory path:

.. code-block:: bash

  msc attest -d /Users/username/directory/ -s 2

To attest the latest state of a Git repository, supply the repository path:

.. code-block:: bash

  msc attest -g /Users/username/gitrepo/ -s 2

Proof retrieval
^^^^^^^^^^^^^^^^^

The client can retrive, store and update sequence proofs for a specified or configured slot position with the `fetch` command. This requires no token or authentication, as the proofs are publicly accessible. All retrieved sequence proofs are stored locally in the application data directory (the location of directory can be found with the ``config`` command), and can also optionally be saved to a specified file (``-f``) or printed to standard output (``-o``). 

To retrieve the full sequence proof for a specified slot from when it was initialised, supply the argument ``-i 0``. E.g. (for slot 2),

.. code-block:: bash

  msc fetch -i 0 -s 2

This sequence proof will then be saved to a file named ``slot_2_sequence.msp`` in the application data directory. 

To retrieve the sequence proof to a specific staychain transaction ID (e.g. ``9eeccf2e6ca6f7257a379debccfb3e822df8658d03c95ec47fbd2267d218f03d``):

.. code-block:: bash

  msc fetch -i 9eeccf2e6ca6f7257a379debccfb3e822df8658d03c95ec47fbd2267d218f03d -s 2

Once a sequence proof for a specified slot has been fetched, it can be updated to include all new slot proofs in the sequence up to the latest with the update ``-u`` argument:

.. code-block:: bash

  msc fetch -u -s 2

Verification
^^^^^^^^^^^^^

The client can perform various independent and trustless verification operations on sequence proofs to confirm the immutability of specified sequences. 

Full verification of a specified sequence proof is performed in two stages, as a sequence proof bridges a secondary system to the Bitcoin blockchain. So typically a user will want to independently verify two properties of a specific sequence proof:

1. That the sequence proof is attested to the unbroken sequence of *staychain* transactions confirmed in the Bitcoin blockchain at the specified slot position. 
2. That the sequence proof corresponds to the sequence of state changes in the external system. 

The client enables users to perform each verification separately according to their individual requirements. Both operations must be successfully performed to verify a unique single history. 

Bitcoin blockchain verification
+++++++++++++++++++++++++++++++++

To verify a specified sequence proof against the Bitcoin blockchain, a connection to a full Bitcoin node must be provided. This is set using the ``-b`` argument, and can be either an RPC URL with authentication details or a public HTTP address (for a remote block explorer). The Bitcoin node can also be set in the client config. For example:

.. code-block:: bash

  msc config -b username:password@localhost:8332

or

.. code-block:: bash

  msc config -b https://api.blockcypher.com/v1/btc/main/txs/

If no node URL is provided, a default public Bitcoin block explorer is used (currently: api.blockcypher.com/v1/btc/main/txs). 

To perform the verification of a sequence proof against Bitcoin, the proof can be supplied as a file (using the ``-f`` argument) or as a JSON object (using the ``-p`` argument). To verify the stored proof in the application data directory, use ``-p 0``. For example:

.. code-block:: bash

  msc verify -p 0 -s 2

If the verification of unique sequence is successful, the client will return the staychain commitment details. For example,

.. code-block:: bash

  Verified proof sequence
  Start commitment in block 00000000000000000002e347026ca276fc5035f637deea48c6386c90504f042b height 604260 at 2019-11-17T21:07:18Z
  End commitment in block 00000000000000000001589123ee33c19e5a7ac8ac8f173867c8f877a7051d16 height 604757 at 2019-11-21T10:17:52Z

If the staychain base transaction ID is also included (the unique identifier) in the configuration, or via the ``-i`` argument, the client will additionally verify that this TxID is part of the staychain. 

State change history verification
++++++++++++++++++++++++++++++++++

To verify that a specified sequence proof corresponds a sequence of state changes, one of the additional arguments of ``-l``, ``-d`` or ``-g`` is used along with the ``-p`` or ``-f`` arguments specifying the proof. The simplest of these is the ``-l`` argument which simply verifies that the given sequence proof matches a specified list of 32-byte commitments. These commitments are supplied hex encoded and comma separated. Fro example:

.. code-block:: bash

  msc verify -p 0 -s 2 -l c635faa8f63f80d40fcc5f764aa3cb2c6de66027682ece03efc499db2edad780,4113d23c6f9dc921bf23f0f551b4cb9909099bbe89464ec7f424b6dabda12924,118d182a45bffea9fd8c6eb98453b6edc19327f6d1f2887b10700d194c275259

If the proof sequence matches the commitment list exactly and in order the client will return the verification:

.. code-block:: bash

  Verified proof sequence against commitment list. 

If the sequence does not match, the verification will fail. 

.. code-block:: bash

  Verification failed. Commitments not matched. 

If all slot-proofs in the sequence proof are matched to commitments in the list in order, but there are additional commtiments included in the list, then the client will return:

.. code-block:: bash

  Verification failed. Additional commitments on list not in proof.

To verify that a specified sequence proof corresponds to a chronological sequence of files in a specified directory, the additional argument ``-d`` is used to specify the directory path. This directory must contain the matching sequence of files, named in an alpha-numeric order corresonding to the sequence of changes. For example:

.. code-block:: bash

  msc verify -p 0 -s 2 -d /Users/username/directory/

If the proof sequence matches the full hash chain of files in the specified directory exactly and in sequence, then the client will return the verification:

.. code-block:: bash

  Verified proof sequence against directory hash chain.

The client will also return a warning if additional files have been added to the directory since the last attestation has been performed. 

.. code-block:: bash

  WARNING: last 1 files not attested.
  Last file attested: document-v0.5.txt

To verify that a specified sequence proof corresponds to the commit history of a Git repository, the additional argument ``-g`` is used to specify the directory path of the Git repository. The client also checks that the initial commit message of the repository is a staychain TxID and slot ID. For example:

.. code-block:: bash

  msc verify -p 0 -s 2 -g /Users/username/gitrepo/

If the proof sequence matches the full hash chain of files in the specified directory exactly and in sequence, then the client will return the verification:

.. code-block:: bash

  Verified proof sequence against commit history to b40a656d028618f6c1d73465c07d810078fd74e4

Where ``b40a656d028618f6c1d73465c07d810078fd74e4`` is the latest Git commit included in the sequence proof. If there have been additional commits to the Git repository since the latest attestation in the sequence proof, the client will return a warnings with the number of non-attested commits. For example:

.. code-block:: bash

  WARNING: last 3 commits not attested.

The ``verify -g`` operation also verifies that the staychain base TxID in the sequence proof, and slot ID, are added as the commit message in the initial commit of the repository. If this commitment is not present, the following warning is given:

.. code-block:: bash

  Staychain ID not committed to Git history

Sidechain synchronization
^^^^^^^^^^^^^^^^^^^^^^^^^^

The client can be used to synchronize a sidechain state against a Bitcoin staychain. This is performed using the ``sync`` command, and requires an RPC connection to both a full Bitcoin node (or trusted block explorer) and the sidechain node. As with the Bitcoin node connection, the sidechain node connection can also be set in the client config:

.. code-block:: bash

  msc config -b username1:password1@localhost:8332  # Bitcoin node
  msc config -n username2:password2@localhost:8336  # Sidechain node

To verify that a sidechain history is unique against Bitcoin's global state, and determine the latest attested sidechain block, the full sequence proof is retrieved, fully verified and then verified against the Bitcoin staychain the the sidechain state. For example: 

.. code-block:: bash

  msc sync -s 1

If the verification is successful, the client will return the latest sidechain verified block. For example:

.. code-block:: bash

  Verified sidechain attestation sequence
  Latest attestated sidechain block: 47e3d796f0ae87f2261e620018ffb1e0458175e17faf2762f209a17c727a8690 height 163188

Key generation and authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The client can generate keys to be used for attestation authentication, and generate commitment signatures using the ``keygen`` command. To generate a 256 bit private key, the ``-g`` argument is used with optional supplied entropy. For example:

.. code-block:: bash

  msc keygen -g entropy

This generated key is then saved in the config and automatically used to sign attestations sent the the Mainstay service URL. The generated hex-encoded private key can then be used to generate the corresponding secp256k1 compressed public key using the ``-p`` argument. 

.. code-block:: bash

  msc keygen -p c76849c6ac48c4996b2847a5b87d9ee0e9463ea11c827591a50978b1b2682804

The returned hex-encoded public key is supplied in the web form used to sign-up to the mainstay.xyz service, if signature based authentication is required. 

Staychain status and information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When initializing a sidechain, Git repository or file repository, the staychain base TxID and slot position must be committed to the initial state in order to prove uniqueness. In a sidechain, this information is committed to the genesis block, and in the case of a Git repository, this information is added as the message of the initial commit. To retrieve the latest staychain TxID to perform this initialisation, the `info` command can be used. 

.. code-block:: bash

  msc info

This returns the base ID. For example:

.. code-block:: bash

  Base ID: 9d049eb88c13d7c4bad6f2597417da525effebc47b2095621b8cebad7ded4cf5:2

The argument ``-c`` will also set this in the config. 

To initialise a Git repository and link it to the staychain and slot position, the initial commit will be as follows:

.. code-block:: bash

  git commit -m '9d049eb88c13d7c4bad6f2597417da525effebc47b2095621b8cebad7ded4cf5:2'
