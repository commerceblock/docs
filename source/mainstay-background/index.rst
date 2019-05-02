Background
============

Trustless Immutability
----------------------

The invention of Bitcoin solved the issue of double-spending in a fully decentralised digital
payments system by ensuring that there is a single, replicated, global ledger that all participants can agree represents the valid ordering of transactions: the blockchain. Reaching
consensus on the state of this global ledger is achieved using proof-of-work: adding to the
blockchain requires expensive, but easily verifiable, computations that are rewarded with
tokens derived from transaction fees (and block rewards). The blockchain with the most
accumulated work is considered the only valid history, and all participants are incentivised
to contribute their computational work to extending it [1].

The use of proof-of-work [2], which requires the consumption of real world resources (i.e.
energy), as the consensus mechanism means that Bitcoin is completely permissionless: no
permission is required in order to add to the blockchain - only computational work. The
work required to extend the blockchain also leads to immutability: any attempt to modify
the time-order of transactions in the blockchain requires more computational power than
the rest of the network combined [3]. This leads to the Bitcoin blockchain being a unique
global system of consensus on the ordering of time-stamped events without the need for any
trusted authority.

Of all the cryptocurrency projects that have been launched since, Bitcoin remains by far the
most secure, with the most accumulated work. The Bitcoin network has operated persistently
for over 9 years, holding hundreds of billions of dollars in value and has resisted constant
attack. However, these properties come at the cost of both scalability and upgradability.
In order to maintain the decentralisation, security and censorship resistance of the network,
block sizes must remain relatively small which limits the transaction capacity [4]: Bitcoin
can process only 3 to 6 transactions per second leading to unpredictable transaction fees and
confirmation times [5]. In addition, for the very same reasons Bitcoin is so secure, it is also
very difficult to change the protocol: adding new features requires the consent of all network
participants and must be done extremely conservatively so as to not risk the integrity of the
system.

Alternative consensus mechanisms on separate blockchains can be used to improve scalability
and build in more advanced features at the protocol level [6]. Sidechains to Bitcoin secured
by federated consensus rules enable significantly better scalability, and much faster and
more regular block times [7]. In addition, these systems can incorporate more protocol-level
functionality, including native smart contracts, asset issuance and cryptographic privacy and
anonymity features not possible on Bitcoin [8]. However, such systems are not able to achieve
the trustless immutability of Bitcoin with permissionless proof-of-work. Blockchains that
are run by a static federated consensus mechanism require collective trust in the federation
members: if the federation members collude or leak a threshold of secret keys, conflicting
forks of the blockchain can be created at no cost and double-spend attacks launched against
token holders [8].

To provide federated sidechains with the same level of trustless immutability as Bitcoin, we
describe a method that involves cryptographically binding these sidechains to the Bitcoin
mainchain in such a way that the sidechain cannot be forked without also simultaneously
forking the Bitcoin mainchain. This means that for a fixed set of federated block signers,
users of a sidechain do not need to trust the federation to protect them from a double-spend
attack: consensus on a single unforked version of the federated sidechain is enforced by
Bitcoin’s proof-of-work.

This protocol - MainStay - employs the underlying concept of a staychain of linked transactions within the Bitcoin mainchain, where all transactions in the staychain are enforced to conform to a single output, preventing branching and any possibility of alternate staychain
histories. By anchoring the staychain transaction ID into the genesis block of the sidechain,
and then committing the state of the sidechain at regular intervals into the staychain, it
becomes impossible to roll back or re-write the state of the sidechain without also rolling
back the staychain, which is effectively impossible due to the might of Bitcoin’s global proof-
of-work. Sidechain nodes can validate these commitments and the resulting immutability of
the staychain using only lightweight SPV1 proofs from full Bitcoin nodes. When a sidechain
block has been committed to a Bitcoin staychain, we say that block is reinforced and is as
immutable as a Bitcoin block of the same depth.

To minimise the encumbrance of the mainstay on the Bitcoin blockchain, and to prevent any
potential miner censorship of transactions containing OP_RETURN outputs, we employ a
homomorphic commitment scheme based on the ‘pay-to-contract’ (BIP175) protocol [9]. In
this approach, commitments from the sidechain are embedded in a single transaction output
address, and the staychain is indistinguishable from normal Bitcoin payment transactions.
We have designed the scheme so that it is compatible with both multisig (P2SH2
) and single public key (P2PKH) addresses, and that no additional hosted data is required in order to
verify the validity of the mainstay. The remainder of this paper is organized as follows:
the next section describes the current state-of-the-art in relation to blockchain attestation
and timestamping. The sections following this then describes the proposed scheme, starting
with the core principles and then specifying the details of the implementation for a federated
consensus protocol. 

Attestation and Timestamping
----------------------------

It was recognised early in Bitcoin’s history that the blockchain could be utilised to timestamp arbitrary data in a completely trustless and decentralised way [10]. By embedding a cryptographic commitment to a piece of data into a valid transaction, which was then
mined into the blockchain, it was possible to prove that the data existed at a particular time
[11]. In the simplest implementation of this idea, the commitment is a cryptographic hash of
the data which is used directly as a payment address (which would be un-spendable). This
approach however led to these unspendable addresses remaining in the UTXO set indefinitely, unnecessarily burdening fully validating nodes. To accomodate time-stamping (and other meta-protocols) in a more efficient way, a new prunable transaction output type was
introduced via a new OP code: OP_RETURN [12]. This allowed up to 40/80 bytes to be
included in an output which was not treated as a spendable output in the UTXO3
set. The use of OP_RETURN however has significant downsides: it bloats transactions (resulting in
higher transaction fees), it offers no privacy (data is included in plain text directly into the
transaction) and transactions including them are often rejected (censored) by some mining
pools.

There are many services that employ OP_RETURN outputs to time-stamp data onto the
Bitcoin blockchain, such as EternityWall [13], Proof of Existence [14] and BlockNotary [15].
Beyond this, there are protocols that can include a much more extensive set of data into a
single commitment, such as OpenTimestamps which collects submitted commitments via a calendar server and compresses them into Merkle Tree, and then time-stamps the Merkle
Root in a transaction. The Factom protocol also time-stamps the state of the Factom system
(including Merkle roots of transaction and other data) into the Bitcoin blockchain at regular
intervals as part of their value proposition [17].
This type of time-stamping is however fundamentally limited in the type of immutability it
can provide. A timestamp can only prove that a particular piece of information existed at
a certain point in time, not that the information has any other validity or provenance. A
timestamp by itself cannot prove that a commitment to conflicting data has not also been
simultaneously timestamped. This is a critical concept in relation to immutability: any
proof-of-existence does not act as a proof that anything else (e.g. an alternative ordering of
transactions) does not also exist. 

Figure 1. Illustration of conflicting (forked) sidechain blocks simultaneously attesting to a
mainchain.

To illustrate this point, we consider a sidechain or alt-chain whose state (i.e. the chain tip
block header) is periodically time-stamped into the Bitcoin blockchain. This does not lead
to immutability of the sidechain, since alternative conflicting states (i.e. forks) can also be
time-stamped simultaneously (see Fig. 1). Any property of immutability then must ensure
that the sidechain state is linked to a specific commitment in the Bitcoin mainchain via
some trusted mechanism - some authority (who may be effectively operating the sidechain)
is responsible for defining the sequence of timestamps that correspond to the un-forked
sidechain. This then relies on the integrity of the commitment mechanism: multiple versions
of a sidechain can be created with multiple simultaneous timestamped commitments into
Bitcoin. This could be used to execute a double spend attack by collusion of a block signing
federation with the commitment authority.

The MainStay protocol is designed to eliminate the requirement for any type of trusted
commitment mechanism and to provide cryptographic proof of sidechain immutability by
initiating a fan-in-only transaction staychain within the Bitcoin blockchain that is uniquely
committed to the genesis block of the sidechain, as described in the next section. The
protocol does not employ OP_RETURN outputs, providing additional privacy, censorship
resistance and efficiency. 