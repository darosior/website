+++
date = 2024-12-18
draft = false
title = "On Soft Forks"
description = "Some thoughts on Bitcoin soft forks, and Bitcoin Core, in light of the recent interest in introducing more expressiveness in Bitcoin Script."
weight = 10
author = "Antoine Poinsot"
tags = ["Bitcoin", "Soft Fork", "Bitcoin Core"]
+++


It appears many have different perspectives on soft forks than i do. Recent events and private
discussions prompted me to share my thoughts on the topic.

Bitcoin's value proposition is to be censorship-resistant money (some like to say "decentralised").
And so far it largely succeeded: 15 years after its inception it's still possible for anyone with
occasional access to the internet to receive, store and send value globally through the Bitcoin
network without having to ask anyone for permission. This is remarkable, and it would be a great
achievement if we can keep it this way for another 15 years. There seems however to be [little
demand](https://transactionfee.info/charts/fees-per-day-btc) for using Bitcoin (at least, as a
medium of exchange). Or maybe just everybody is using Lightning and channel closures are a thing of
the past. Some believe many would like to use Bitcoin but are simply unable to, and this seems to be
one motivation behind the recent soft fork excitement. Another common, presumptuous, explanation is
that actually a lot of people need Bitcoin, but *they don't realise it*. It's necessary for the
western bitcoiner, well educated in the Bitcoin cult, to come and coinsplain them why they need to
accept Bitcoin in their heart despite having already made the choice it was not the best option for
them. We should note however that a low usage of Bitcoin can be viewed as a good thing: the contrary
may imply large scale economic repression is happening.

Bitcoin achieves its goal of being censorship resistant money through coordination. Users coordinate
around a fixed set of rules defining the money. These rules are embedded in software: a Bitcoin
client (or node). One rule defines how clients coordinate to agree on the state of the system:
transactions are ordered in a chain of PoW. Most realise how the censorship-resistant coordination
permitted by mining is fundamental to Bitcoin. It is important to take a step back and also realise
how amazing it is that millions across the world today coordinate around a fixed set of rules for
their money. This happened progressively as Bitcoin grew, and it's hard to see how it could be
replicated today. We need to appreciate how fragile is this coordination of millions of people
spread around the world, with different backgrounds, cultures, incentives, on something as political
as the rules defining their money. Polically as much as technically, consensus is the most important
feature of Bitcoin and **we must be very careful in risking it**.

A soft fork is an update to the consensus rules defining Bitcoin. It is technically a
backward-compatible update, making it much [safer to roll
out](https://gnusha.org/pi/bitcoindev/CAPg+sBjJcqeqGLHnPyWt23z3YoCRGozQupuMxy51J_-hdkKBSA@mail.gmail.com)
on the network.  However, it is still an update to the rules defining the money. The "soft"
qualifier, while well deserved from a technical standpoint, had the unfortunate consequence of
making it sound less politically dangerous to some. It is not.

In the past couple of years, a number of Bitcoin enthusiasts and developers published proposals to
introduce new functionalities to the Bitcoin scripting system. A common complaint emerging from this
group (both [formulated publicly](https://x.com/jamesob/status/1857049961235403101) or rapported to
me in private discussions) was the lack of engagement of protocol developers, and especially of the
fetishised Bitcoin Core maintainers, with their proposals. I have reasons to provide for the lack of
engagement, but before that i want to address the Bitcoin Core attacks and criticims.

In a broader context, i believe much harm arises from attributing intentions to groups of people.
Sure, people engage in groupthink. But at the end of the day each individual has his own interest,
motives **and responsibility**. The same is true for the emergent group of developers happening to
be contributing to the Bitcoin Core open source project. There is no loosely defined "Core devs" to
blame. It is also important to realise how small the group of contributors is compared to the
criticality of the project and the many areas it covers (cryptography, validation engine,
mempool/mining, transaction and blocks propagation, nodes addresses management, indexes, wallet,
GUI(s), build system(s), utilities, ..).  Even smaller is the group of contributors actively working
on "protocol stuff". And it's not like there is a shortage of important problems to tackle, despite
what some may say.

Maintainers in particular take a lot of undeserved shit. Note they are also contributors working on
their own projects: Ava on improving the wallet (such as to [use the features enabled by the last
soft fork](https://github.com/bitcoin/bitcoin/pull/29675), which we are still far from taking full
advantage of), Gloria on package relay (to secure the second-layer protocol(s) enabled by the
second-to-last soft fork), Michael on the toolchain (so Bitcoin survives one more release cycle
without being backdoored a la [`xz`](https://en.wikipedia.org/wiki/XZ_Utils_backdoor)), and Russ on
multiprocess (to be more robust against situations like
[this](https://bitcoincore.org/en/2024/07/03/disclose_upnp_rce/)). But when acting as maintainers,
they are mainly directed by what other contributors are interested in working on. They coordinate
and facilitate activitites of the various contributors and, to be honest, take care of a lot of
things regular contributors are often less concerned with (broken CI, dependencies management,
releases and backports, codesigning, spam on the repo, ...).

Therefore maintainers' activity largely reflects what the larger group of contributors is interested
in. If you are mad at a maintainer for not engaging with your pet project, it only talks to your
inability in getting any single of the Bitcoin Core contributors interested in your proposal.

With that out of the way, let's now discuss the lack of interest in the recent proposals. I can only
talk for myself, although others may agree with (parts of) the analysis. It is also worth to
explicitly mention not all contributors are ignoring all proposals: some individuals who contribute
to Bitcoin Core did engage with some proposals, or even came up with their own. I did take part in
some of the discussions, albeit less so in the past year. Anthony Towns developed and [ maintains a
whole implementation](https://github.com/bitcoin-inquisition/) dedicated to testing proposals. Greg
Sanders [went through all the work](https://delvingbitcoin.org/t/ln-symmetry-project-recap/359) of
making an Eltoo proof of concept: design the scripts to be used, design the protocol as an extension
of the existing [BOLTs](https://github.com/lightning/bolts/), actually implement it and start
discussions (themselves backed by concrete proposals and implementations) about the necessary
changes to the transaction relay protocol to make such channels more robust. This is so far the most
extensive work i have seen supporting any of the existing proposals, but this is not something you
will learn from listening to the full-time Twitter soft fork influencers.

A first reason for my lack of engagement is that there has been a lot of motivated reasoning of the
kind "we need a soft fork; X is a possible option; let's do X". A common variant has "it's been too
long since the last soft fork" as its first step instead. This couldn't be farther from my vision of
Bitcoin. A soft fork is a risky, costly, mean to an end. We should not do a soft fork for the sake
of doing a soft fork.

A related pattern i have noticed is what i dubbed the "irrational intervention". It goes like this:
"Bitcoin is imperfect; we must change Bitcoin; X is a possible option; let's do X". I think this is
an emotional response to witnessing a suboptimal situation, and succumbing to this strong appeal to
"do something about it". There is also the "try something" variant which explicitly acknowledges the
person has not rationally assessed whether X is at all a solution to the problem identified. It's
hard not to draw a parallel between this attitude and the [block size debates from 10 years
ago](https://blog.bitmex.com/the-blocksize-war-chapter-1-first-strike/). We should not do a soft
fork without a careful analysis of its costs and benefits.

This leads us to a third reason, probably the most common. It seems most proposals are a solution in
search of a problem.  Don't get me wrong, these are often interesting to think about, and usually
neatly tie into existing research and constructions.  But until their champion(s) can make a strong
case that they will bring tangible improvements to real-life Bitcoin users, they are not reasonable
to consider for a soft fork. It is not true of all proposals, as some were designed to address a
specific usecase. I [have looked into
some](https://delvingbitcoin.org/t/using-op-vault-for-recovery/150) but none really convinced me
they were worth changing Bitcoin's consensus rules. And i say that despite having a strong financial
interest ([1](https://wizardsardine.com/revault/), [2](https://wizardsardine.com/liana/)) in some of
them making it in.

Another reason is that time is in fact scarce, and people's imagination for Script opcodes
unlimited. Nowadays everyone and their cat have a soft fork proposal. My recently joining [Chaincode
Labs](https://chaincode.com/) was motivated in parts by having more time to keep up with the
research in this area. Still, we all need a way to prioritize which proposals to give attention to,
among other important works which also need our attention. I have to say the pretty low SNR of some
discussions in the past couple of years led me to realise my time was better spent elsewhere. A
mailing list is a 1-to-N mechanism. If someone is going to post to the mailing list, it's reasonable
to expect them to be familiar with previous research on the topic, to not be rehashing previous
discussions without bringing anything new to the table, to be presenting their ideas to be
understandable to a reader who does not have all the context and to not be sharing half-baked ideas
that take longer to debunk than to broadcast.

I am excited by the prospects of all the energy invested in exploring the design space of extending
Bitcoin's scripting capabilities. If someone comes up with a way to make Bitcoin significantly more
accessible or more robust, a soft fork should definitely be on the table. But the upsides must
compensate for the costs of a soft fork. Bitcoin users is a much broader group than Bitcoin
enthusiasts, let alone than Bitcoin Twitter. And coordinating on changing the rules of all of these
people's money is inherently political. There is nothing wrong with discussing soft forks, on the
contrary. More research is better. But to be taken seriously, anyone suggesting to activate anything
needs to acknowledge a soft fork comports substantial political and technical risks to a network
that millions depend on for their censorship-resistant payments and that secures trillions of
dollars worth of other people's money.
