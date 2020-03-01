File repository guide
======================

This guide describes how to link a *file repository* to the Bitcoin blockchain via the *mainstay.xyz* connector service, in order to obtain a trustless proof of a unique single history of of the sequence of files added to the repository. The repository is defined by a specified directory on the users system, and the user is able to unambiguously prove to anyone the order in which documents or other files are added to this directory, and that only these files have been added. 

The file-repository protocol creates a proof of an immutable sequence for files/documents by concatenating the SHA256 hashes of an ordered set of files, and then attesting the SHA256 of this concatenation to the *mainstay.xyz* slot each time it is extended with a new file. This data structure enables the generation of proof both of the order of files added to the repository over time and the unquineness of the files in the repository. This proof can be used to demonstrate the immutability and uniqueness of the version history of a file or document. 

The guide is split into three distinct sections for setting up, performing repository attestations by the permissioned entity and verification. 

Initial set-up
^^^^^^^^^^^^^^^

The entity responsible for attesting the file repository to Bitcoin is called the *administrator*. The administrator is responsible for the agreement with the *mainstay.xyz* service for exclusive use of a connector service slot. This is obtained by signing up on the *mainstay.xyz* website, where the administrator will be given a slot position ``slot_id`` and an API access token to perform attestations ``token`` via email. For additional security, the administrator can optionally require that all file repository attestations committed to the slot are signed by an administrator private key (the corresponding public key being provided on sign-up at *mainstay.xyz*). 

To generate this key, the administrator can use the Mainstay client. For example (where entroy is any string to add additional entropy to the keygen),

.. code-block:: bash

  msc keygen -g entropy

  > Private key: c66cb6eb7cd585788b294be28c8dcd6be4e37a0a6d238236b11c0beb25833bb9

This generates a hex encoded 32-byte private key (``privkey``). The corresponding public key can be generated as follows:

.. code-block:: bash

  msc keygen -g c66cb6eb7cd585788b294be28c8dcd6be4e37a0a6d238236b11c0beb25833bb9

  > Public key: 03fe0c17d00e5d5fc879dd1c2d4fbe3d61eb6d851ce3cd31173219aaf72e13fcd9

The generated public key is then supplied at sign-up. The private key is stored in the ``msc`` config file. 

Once the *mainstay.xyz* slot is active, the **base** staychain Bitcoin transaction ID can be retrived as follows (e.g. for ``slot_id = 3``):

.. code-block:: bash

  msc info -s 3

If the slot has been activated (usually within an hour of sign-up completion), the base ID will be returned. E.g. 

.. code-block:: bash

  > Base ID: 420d083de8ab078dbba5ea37f877cb35dd621e34f231cce997a16cd241449d51:3

If not active, the the following message will be returned:

.. code-block:: bash

  > Slot 7 not active.

If the purpose of the file repository is to prove that a published document has only a single possible version history, then the ``Base ID`` should be committed (i.e. included) to the document (e.g. either as part of the visible main document or as file info metadata). 

The administrator then creates a directory for the file repository on their system with path ``repo_path``. Files can then be added to this directory, but they must be added with filenames in **alphanumeric** order in order for the attestation and verification to operate correctly. E.g. for a single document version history, the successive versions of the document should be named in alphanumeric order (e.g. ``version_1.0.doc``, ``version_1.1.doc`` etc.). No files should be deleted from the repository, and the repository directory should be regularly backed up - loss of any files will prevent the proof from being verified. 

Attestation
^^^^^^^^^^^^

Each time the repository is updated with a new file version, the administrator can perform an attestation to the *mainstay.xyz* slot. This requires a single call of ``msc`` with the path of the repository directory supplied with the ``-d`` argument of the ``attest`` command. E.g. 

.. code-block:: bash

  msc attest -d repo_path -s 3 -t token

Verification
^^^^^^^^^^^^^

The previous steps are performed solely by the administrator. Verification can be performed by anyone who has access to the full document history (i.e. the contents of the document repository). The first step is to copy the full repository to a directory ``repo_path`` on the verifiers machine. The verifier also requires the Mainstay client installed (``msc``) and RPC access to a full ``bitcoind`` node (alternatively a trusted block-explorer API can be used). 

The initial step in the verification process is to retrieve the full proof sequence for the slot to the base ID of the repository. This is done with the ``fetch`` command and the ``-i`` argument specifying the base TxID (and the ``-s`` argument specifying the slot ID). 

.. code-block:: bash

  msc fetch -i 420d083de8ab078dbba5ea37f877cb35dd621e34f231cce997a16cd241449d51 -s 3

Which returns information on the attestation history. E.g.:

.. code-block:: bash

  Sequence length: 36
      Start: 7 Feb 2020 11:34:41
      End: 10 Feb 2020 00:21:01

This retrieves the full proof sequence from the latest attestation to the base ID in the first commit of the repository. This sequence proof is saved in the msc data directory (which can be found by running ``msc config``). The sequence proof can also be saved to any other file using the additional ``-f`` argument. 

After fetching the sequence proof, it is then verified in two stages: 1. Verification that it is fully committed to the valid (i.e. single output) Bitcoin staychain in the specified slot position. 2. That the slot commitments correspond fully (and in order) to the file repository commit sequence. 

The first stage is performed with supplied ``bitcoind`` RPC credentials and URL (as ``-b``) and the slot ID (``-s``). 

.. code-block:: bash

  msc verify -s 3 -b username1:password1@localhost:8332 -p 0

The argument ``-p 0`` specifies that the sequence proof to be verified against Bitcoin is located in the data directory. Alternatively the path of the sequence proof can be provided (``-p proof_path``). If the verification is successful, the client will return the latest sidechain verified block. For example:

.. code-block:: bash

  Verified proof sequence against staychain base 420d083de8ab078dbba5ea37f877cb35dd621e34f231cce997a16cd241449d51 slot 3
  Start commitment in block a883c7cb269fd5767aebdf60691eaf4056f36a84fca99ebb1c1632c511626b3c height 601563 at 7 Feb 2020 11:34:41
  End commitment in block fa968812fb93f5c949d13021881179e4398582ef650dc1a9e975951f9d411906 height 601611 at 10 Feb 2020 00:21:01
  End commitment txout unspent

In addition, the client will state whether the last (``End``) commitment is in an unspent Bitcoin transaction. If it is not, there may be further commitments not reflected in the repository - this may be because a new attestation has been performed since the sequence proof was fetched. If this is the case, run ``msc fetch -u -s 3`` to update the update the sequence proof to the latest attestation, and repeat the verification. The stored sequence proof will then be updated to include the Bitcoin block heights of each attestation. 

Once the validity of the full sequence proof against the Bitcoin blockchain has been established, the second stage of verification is to confirm that the sequence of commitments to the specified slot position correspond to the repository file hashes in a single, unbroken sequence. This is performed by using the ``verify`` command with the ``-d`` argument specifying the file repository path. E.g. 

.. code-block:: bash

  msc verify -s 3 -d repo_path

If verification is successful, the client will return:

.. code-block:: bash

  Verified proof sequence directory hash chain

If information on the individual attestations of the sequence are required, the same command can be run with a higher verbosity ``-v``, e.g.

.. code-block:: bash

  msc verify -s 3 -d repo_path -v

The for each attestation in the sequence, the following is returned:

.. code-block:: bash

  Commitment 2eaa89dfca418c6bacf99ba9f54ba43203170159d85d6d3faf5783e11e741384
  Latest file version_1.23.doc
  In TxID f9499ca0c2125b4eda9490f50866fd3b9d5ff4b96b3224fac4972cc23ba67685
  Block height 534875

If additional files have been made to the repository directory since the latest attestation and that are not part of the verified sequence proof, a warning is given. E.g.  

.. code-block:: bash

  WARNING: last 2 files not attested.

