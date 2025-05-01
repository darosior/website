+++
date = 2025-05-01
draft = false
type = "blog"
title = "On relay policy and recent OP_RETURN drama"
description = "Relaxing restrictions on OP_RETURN is fine. Bitcoiners need to stop being so gullible."
weight = 10
author = "Antoine Poinsot"
tags = ["Bitcoin", "Bitcoin Core", "Policy"]
+++

I recently [emailed][bdev email] the Bitcoin development mailing list about lifting the restrictions
on the use of `OP_RETURN` outputs in standard transactions. This triggered yet another shitstorm,
which prompted me to lay down my thoughts on the topic as a blog post to minimize the time wasted
arguing in parts on Twitter.

## What is relay policy and why is it necessary?

Policy is extremely broad and refers to all the rules observed by the software when interacting with
the Bitcoin P2P network. Examples include the organization of requests emitted to peers, delays in
message processing, refusing to process blocks with a timestamp more than 2 hours in the future
(compared to the system time), or refusing to process certain types of transactions. The object of
recent drama is the latter. I will use the nomenclature from my [Bitcoin Stackexchange answer][SE
answer] on the topic and refer to the rules dictating the format of transactions in isolation as
"transaction standardness", which is a subset of "mempool policy", itself considered a subset of
"transaction relay policy", finally a subset of "relay policy".

Standardness rules exist for three main reasons. The first is as DoS protection. Your peers on the
P2P network are not identified and some transactions are more expensive than others to process. An
asymmetry between the cost of sending you an unconfirmed transaction (which is very small) and the
cost for you to process it creates a DoS vector. Another reason for standardness rules is to provide
upgrade hooks for soft forks. Invalidating a type of transactions in a soft fork that was already
non-standard for a while gives more guarantees that a non-upgraded miner won't include a
newly-invalid transaction in a block, making soft forks significantly safer to roll out. Finally,
standardness rules have also been used as a way to nudge behaviour toward a less harmful approach,
or to mildly deter certain activities. For instance by standardizing `OP_RETURN` outputs, or
discouraging data storage back when [blocks were not full][Pieter post].

## What are the current standardness rules?

An unconfirmed transaction will be dropped by Bitcoin Core if:
- it's invalid by consensus;
- it is timelocked to any future block but the next block;
- its version is anything but 1, 2 or 3;
- it's larger than 400k weight units;
- it's smaller without witness than 65 bytes;
- any of its inputs has a too large or non-pushonly scriptSig;
- any of its inputs spends a scriptpubkey that does not match any script template from a shortlist;
- any of its inputs spends a legacy P2SH scriptpubkey which contains too many signature operations;
- any of its inputs spends a Segwit P2WSH scriptpubkey which contains a too large witness script;
- any of its inputs spends a Taproot output with a witness stack which contains an annex;
- any of its inputs' witness stacks contain a too large element;
- any of its outputs has a scriptPubKey which does not match any script template from a shortlist;
- if more than one of its outputs matches the `OP_RETURN` ("datacarrier") template;
- any of its outputs has a below-dust value;
- it requires validating too many signatures overall;

## What modification is proposed? Why?

I recently suggested lifting the restrictions on the usage of `OP_RETURN` outputs in standard
transactions. "`OP_RETURN`s" are a provably-unspendable type of outputs that were made standard in
response to the usage of regular output types to store data in the Bitcoin blockchain (that is,
non-transaction data). The goal was to provide a less harmful but as convenient method to store the
data, that good actors could pick up. This was [successful][b10c op_returns]. Along with the
standardization of this new output type, the size of the associated data was limited as a mild
deterrent against storing too much data.

Mild deterrent because these are otherwise valid transactions, that the node would accept as part of
a block. Most of the public transaction relay network enforcing a policy of filtering too large
`OP_RETURN` outputs could deter isolated demand for those, or nudge developers to design their
applications to take these limitations into account. But it is **ineffective** to prevent this type
of transactions from being used if there is significant demand for them. Worse, treating a widely
used type of transaction as non-standard is actually **harmful** from both the perspective of a node
runner (blinding them to part of the network activity) and that of the network at large (hurting
optimistic block reconstruction).

For a long time, `OP_RETURN`s remained the primary mean for people to store a small amount of data
onchain. A few years ago people started leveraging the Segwit discount and storing data into an
input's witness instead. This let them store more data at a lower cost. A trend started around [this
type][inscriptions doc] of transactions, which became in high demand. In fact so much so that it
became the main driver of block space demand for a while. Although this approach already permitted
to store ~5000 times more data in a single standard transaction, the demand for inscription
incentivized people to build [private bridges][slipstream] to miners to bypass other standardness
limits such as the maximum transaction size in order to store even more data.

We are now in a situation where Bitcoin Core's restriction on the size of `OP_RETURN`s by
standardness is not binding anymore. One can store ~5000 times as much data in a standard
transaction by stuffing its witness. Private bridges to miners and alternative public relay networks
were built that allow to store ~50k times as more data in a single transaction. In fact these
alternative relays allow bypassing several of Bitcoin Core's standardness rules, including the
maximum size of `OP_RETURN`s!

While these restrictions are not binding anymore for whoever wants to store data onchain, they still
unnecessarily restrict constructions with time-sensitive transactions. Protocol designers want those
transactions to be standard for a good reason: they don't want to rely on private bridges for
security-critical transaction broadcast. Those transactions need to relay properly on the more
censorship resistant public network. It was recently brought to my attention that [Citrea][citrea]
faced this situation with their [Clementine bridge][clementine]. In their construction, a watchtower
challenge transaction needs to both pass on 144 bytes of data and be confirmed as soon as possible.
Since they are restricted to a single 80 bytes `OP_RETURN` output by Bitcoin Core standardness, they
use two unspendable Taproot outputs to include the other 64 bytes of data. These Taproot outputs
will remain unspent forever, and every single full node will have to keep them in its UTxO set
forever.

In the specific case of Citrea, it's unlikely that many of those outputs would end up onchain. But
it illustrates a severe issue in Bitcoin Core's standardness rules. Not only they do not provide
binding constraints to prevent using the block chain to store arbitrary data, they also incentivize
more harmful ways of storing data! This is why i proposed to drop those restrictions.

## What about optionality? Do you hate freedom?

Peter Todd's [pull request][PR 32359] to drop these restrictions also removes the accompanying
startup options (`-datacarrier` and `-datacarriersize`). This was quickly picked up as a narrative
by trolls in the thread as well as on social media: not only does Bitcoin Core changes the default,
it takes its users hostage by preventing them to turn it back on!

This is obviously nonsense when you think about it for a minute. These options are one of the only
knobs available to a user to tweak transaction standardness rules, let alone relay policy. Would it
make sense for Bitcoin Core to provide startup options to tweak other aspects of policy? Should we
add an option to let the user pick what transaction version should its node consider standard? An
option to accept transactions that are timelocked up to `N` blocks in the future in its mempool? To
disable wtxid relay? Obviously not. But why? And how do we think about it consistently?

For one, options do not come for free. They increase code complexity and cognitive load on the user.
Additional load means reduced ability for the user to make an informed decision about every single
option. So the question, really, is when should we have an option? I think the answer to that is
"when we can reasonably expect users to have a good reason to change the default, and more so than
for other options we could provide instead".

According to this criterion we've established, what can we say of `-datacarrier` and
`-datacarriersize` in a world where there is no restriction on `OP_RETURN`s by default? First of all
defaults are sticky. The vast majority of people do not change the default, so we can't really
expect providing the startup option to have any global effect. The local effect of a user setting
`-datacarrier=0` or `-datacarriersize=X` on their node would effectively just be shooting themselves
in the foot: they would willfully blind themselves to part of unconfirmed transaction activity. If
they are a miner this could increase roundtrips for compact block reconstruction. If they are
transacting, this would prevent them from seeing part of transactions they are competing with
for block space.

Once the default is set to not impose restrictions on the use of `OP_RETURN` outputs, there is no
reason for a user to change the default. Therefore i don't think a startup option to do that should
exist.

Freedom is good. Having a startup option for every single operation that pertains to relay policy is
not that.

## Is Bitcoin dead?

No.

Many have taken the opportunity of my proposal to grandstand on Twitter. They exploit gullible
people by making wild claims about `OP_RETURN` outputs being an existential threat to Bitcoin. I
hope my post made it clear it is not, but consider also this. If Bitcoin was so brittle that Core
choosing to relay perfectly safe, inexpensive to validate, consensus-valid transactions would break
it, it would be utterly uninteresting in the first place!

The ability of this parasitical group to reach this level of attention is really telling of what our
community values. I hope bitcoiners start demonstrating more critical thinking and stop giving in to
the latest conspiracy theory every 6 months.

## Is Bitcoin Core compromised?

No.

You should be highly skeptical of anyone making this claim. It undermines Bitcoin's long term
success.  Cultural incentives matter. It is about time that we stop tolerating nonsense and come
back to a place where most of us value (proof of) work, ideas, and selflessness instead of overblown
emotional reactions, unbacked wild claims and adhesion to various cults.

For more on Bitcoin Core i highly recommend Adam Jonas' "[Has Bitcoin Core lost focus?][Jonas MIT]".

[bdev email]: https://gnusha.org/pi/bitcoindev/rhfyCHr4RfaEalbfGejVdolYCVWIyf84PT2062DQbs5-eU8BPYty5sGyvI3hKeRZQtVC7rn_ugjUWFnWCymz9e9Chbn7FjWJePllFhZRKYk=@protonmail.com
[SE answer]: https://bitcoin.stackexchange.com/a/120271/101498
[Pieter post]: https://gnusha.org/pi/bitcoindev/QMywWcEgJgWmiQzASR17Dt42oLGgG-t3bkf0vzGemDVNVnvVaD64eM34nOQHlBLv8nDmeBEyTXvBUkM2hZEfjwMTrzzoLl1_62MYPz8ZThs=@wuille.net
[b10c op_returns]: https://transactionfee.info/charts/output-opreturn
[SE inscriptions]: https://bitcoin.stackexchange.com/a/117019/101498
[inscriptions doc]: https://docs.ordinals.com/inscriptions.html
[slipstream]: https://slipstream.mara.com
[citrea]: https://citrea.xyz/
[clementine]: https://github.com/chainwayxyz/clementine
[PR 32359]: https://github.com/bitcoin/bitcoin/pull/32359
[Jonas MIT]: https://youtu.be/TqxDr_SjAgg?t=19080
