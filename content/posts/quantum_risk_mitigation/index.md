+++
date = 2026-06-05
draft = false
type = "blog"
title = "Quantum threat: let's not turn Bitcoin into an altcoin"
description = "A case for mitigating quantum-computer risk without compromising Bitcoin’s core properties."
weight = 10
author = "Antoine Poinsot"
tags = ["Bitcoin", "Quantum", "Soft Fork"]
+++

# Quantum threat: let's not turn Bitcoin into an altcoin

Cryptographically Relevant Quantum Computers are an uncertain, existential threat to Bitcoin.
Discussions about addressing this issue raise fundamental questions about the nature of Bitcoin, as
a consensus system and as a currency. Some trends in the discourse around this issue concern me, so
naturally i figured one more blog post would fix that.

I believe there is something that sets Bitcoin apart from technically similar competing
alternatives. Apparently, so does the market. The reason for this boils down to network effects:
people believe Bitcoin is useful because other people believe Bitcoin is useful. And the cornerstone
of usefulness is reliability: most people expect to be safer storing value in Bitcoin than in any of
the competing alternatives. I believe this expectation largely matches reality, but the way Bitcoin
approaches the CRQC threat could drastically change this.

Two possible responses to this risk could undermine trust in Bitcoin so severely as to make it
indistinguishable from an altcoin. The first one is that a large portion of the userbase gets their
coins stolen, as that would be a fatal blow to network effects. The other one is that the vast
majority of the economy coordinates to pre-emptively freeze other people's coins, deemed to pose a
systemic risk if a CRQC materializes, as that would directly undermine Bitcoin's value proposition.
It is important to stress that the latter is not merely an ideological position: confiscation being
impractical is the whole reason why users trust Bitcoin to have value in the first place. Break this
trust, and you'll eventually lose the network effects.

These failure scenarios may only be avoided if the vast majority of Bitcoin users have migrated to
post-quantum output types by the time, if ever, the Bitcoin economy believes a CRQC will imminently
materialize. Therefore a migration strategy should attempt to maximize the number of individual
users upgraded before Quantum Panic day (QP-day?). This is an enormous undertaking, because most
Bitcoin users are not active Bitcoin enthusiasts or companies. They don't keep up with the latest
developments, rarely move their coins, and even more rarely upgrade their custody setup. This means
that in the scenario where a CRQC eventually materializes, migration must take place during the
period of uncertainty, as once we knew for sure it was going to happen it would already be too late
to migrate the long tail of Bitcoin users.

To make matters worse, the PQ signature scheme would itself be more cumbersome to use and several
common practices that underpin today's Bitcoin usage would be unavailable[^PQ-hash-scheme]. This is
a typical collective action dilemma: everyone would greatly benefit from everyone migrating (in the
event a CRQC does materialize), yet every individual's benefits to unilaterally migrate are
low[^uni-mig] and associated costs are high. In light of this, i think it's useful to classify
migration strategies in two categories: strategies whose costs are borne individually vs strategies
whose costs are borne collectively.

In the first category i would include P2QR[^P2QR], an output type that makes a PQ signature scheme
available *exclusively*. This maximizes the (limited[^uni-mig]) individual benefits if CRQCs become
a reality, as it guarantees the owner retains access to his coins on any chain that survives. It is
also the only option that is effectively a full migration, which is useful in avoiding "marketing
upgrades" whereby a service provider adopts the official PQ migration output types while still
leaving its users dependent on EC security assumptions[^marketing-upgrade]. Of course, the downside
of this approach is that individual users must bear the costs of upgrading long before anyone knows
whether it is at all necessary. This strategy therefore does not satisfactorily address the "long
tail" problem.

In the second category i would include P2TRv2[^P2TRv2], which prescribes introducing a Taproot clone
with a PQ signature scheme available in leaf scripts, so users can attach a PQ escape hatch to their
coins at no cost, to later complete the migration by disabling EC operations in this output type
if/when the CRQC threat materializes. Because it is no more expensive to use, wallets would start
defaulting to this output type and progressively migrate the long tail of users over the next
~decade[^seed-pq]. As time passes, more information about the progress of CRQCs can guide the
next steps.

This strategy embraces the fact that we simply do not have a good replacement for EC-based
signatures. Making access to CRQC resistance depend on introducing significant friction in (or giving up
entirely) established workflows and use cases will lead to users not migrating until the CRQC threat
materializes. This would not give the long tail of Bitcoin users enough time to migrate before
QP-day and therefore leave the system vulnerable. Since Bitcoin still systemically relies on EC
security unless the vast majority of users migrate, P2TRv2 leverages Taproot's hidden spend path
feature to eliminate the individual costs of opting into the migration, and lets its completion be a
later consensus change.

And even if we had a good replacement for EC cryptography, the P2TRv2 strategy would also shine on
two fronts. First, it defers the step that comes with more downsides for Bitcoin until the CRQC
threat becomes less speculative[^no-worse-off]. This is important even if you are convinced CRQCs
are coming, because a more consensual first step may let the migration begin sooner. Secondly, it
allows the work of porting to the new signature scheme the wide range of protocols, standards and
use cases that can be salvaged to proceed in parallel with the migration period.

Why not do both then? On the surface, this seems compelling: those who can afford to fully migrate
to the more cumbersome PQ scheme today can, and others who prefer to opt into the escape-hatch
solution can as well. Yay, more optionality! Well, not really. One of the options is critical to the
success of the migration, since it gives a chance to migrate the long tail of users, while the other
one is more of a nice to have. Actually the more credible EC disabling in P2TRv2 becomes, the more
the P2QR strategy becomes a waste of onchain space rather than a nice to have. And having the P2TRv2
output type as the Schelling point of the migration virtually guarantees that EC operations will be
disabled in this output type: otherwise literally every active user risks losing their funds. Thus
having P2QR as a second option alongside P2TRv2 would only create more risks by weakening P2TRv2 as
the Schelling point of the migration.

Various alternative strategies have been proposed based on the P2MR output type. First introduced in
the 8th revision of [BIP 360][BIP360], P2MR is a clone of Taproot but without the key path spend. I
will discuss here the strategy i have seen most widely defended by proponents of P2MR: doing BIP 360
alongside the introduction of a PQ signature scheme as a Script operand. The motivation is similar
to that of P2TRv2: let users commit to a PQ spend path while still being able to use the cheaper EC
spends. The big difference is the removal of the key path in an attempt to mitigate "long range"
attacks. This difference unfortunately makes migration more expensive by about 15%[^15%] and
preemptively gives up the Taproot [incentive structure][sipa-taproot], key feature of the last
Bitcoin upgrade. I believe this is the wrong tradeoff.

The long-range vs short-range distinction is at the heart of the debate around the PQ migration
strategy. I believe most of the disagreements come down to whether we should see this distinction as
meaningful. From the point of view of an individual Bitcoin user, it certainly is meaningful: in a
world where your public key becomes a secret, sharing it on a global public broadcast network
clearly will increase your risk. But this does not mean that the distinction is meaningful when
considering the Bitcoin system as a whole. Public keys are already assumed to be, well, public
throughout widely adopted workflows. Users trust that the discrete logarithm problem is hard when
their hardware wallets share their xpub with online devices, when their wallet software shares their
xpub or output descriptor with their wallet provider, when they share their xpub with a payee, when
they publish a silent payment address or a paynym, when they reuse or spend from a hashed-pubkey
address, etc.. In these conditions, whether some users have their public keys hashed onchain before
spending time is irrelevant to the PQ migration of the Bitcoin system.

To fully migrate Bitcoin away from relying on EC security, the P2MR strategy would therefore need to
be complemented by a later disabling of EC operations, just like P2TRv2. But besides making it more
expensive for users to opt into the migration output type, it also incentivizes using it for
[purposes][steven-p2mr] other than PQ migration. This makes it harder to believe that 1) enough
users would migrate in the first place and 2) the EC disabling would in fact happen, since it would
raise confiscation concerns. For these reasons, and because i don't think we should downgrade
Bitcoin until CRQCs materialize, i think P2TRv2 is a superior solution to P2MR.

In conclusion, the goal of a PQ migration should be to allow Bitcoin **as we know it** to survive a
break of the discrete logarithm assumption. Transforming Bitcoin into yet another altcoin by
permitting widespread theft, or by enabling confiscation, is not an interesting goal. The major
challenge is that each individual Bitcoin user would strongly benefit from everyone else migrating,
while migration itself is individually costly and would need to begin long before we can be
reasonably certain whether the CRQC threat will materialize. The P2TRv2 strategy addresses this
issue by shifting the migration costs from individual users opting into using the new output
type in the short term, to ~all Bitcoin users coordinating to update consensus rules in the
medium to long term. And P2TRv2 variants that allow users to avoid revealing their public
key onchain before spending make, in my view, the wrong set of tradeoffs.

[^PQ-hash-scheme]: Mikhail Kudinov and Jonas Nick, [“Hash-based Signature Schemes for Bitcoin”][PQ-hash-paper]
[^uni-mig]: Why should one migrate if nobody else does? One response may be to have coins available
on an altcoin that bootstraps off of Bitcoin's UTxO set, but it says nothing of the value of said
coins on whichever chain survives. This also ignores another option available to many users: just
selling (part of) their coins to hedge against the CRQC risk.
[^P2QR]: I steelmanned the case for P2QR [here][P2QR-steelman-link], Pieter also previously
suggested something like P2QR [here][P2QR-sipa].
[^marketing-upgrade]: Since "Post-Quantum" is a valuable marketing label currently, and most of the
costs associated with migration arise not from using a different output type, but updating all your
workflow with the new signature type that does not rely on EC security, this seems likely to happen.
[^P2TRv2]: First proposed by Matt Corallo [here][P2TRv2-bluematt].
[^seed-pq]: This is possible because the vast majority of wallets use a single-key spending policy
and follow BIP 32 to deterministically generate keys from a master key. This BIP 32 master key is
itself derived from a seed using a hash function (the seed is itself generally derived from mnemonic
words using BIP 39). This makes it possible to securely use a PQ key derived from the BIP 32 seed
even if EC cryptography is broken.
[^no-worse-off]: There are of course *some* downsides to this first step, such as introducing a new
output type. But these downsides are marginal compared to those of alternative strategies, and also
come with benefits in the case where CRQCs never materialize, such as having more users migrated to
Taproot.
[^15%]: Calculation based on a 2-input, 2-output transaction for a typical single-key spending policy.

[PQ-hash-paper]: https://eprint.iacr.org/2025/2203
[P2QR-steelman-link]: https://gnusha.org/pi/bitcoindev/0vqF88LoOnY4GiUB4vf-MdeZpTAtR70tokS3cLwt2DX0e6_fD1X_wyhPwWEdIdm6R88AULObIU08CWsb5QfeoaM5c4yXPqN5wHyCrqMCtfQ=@protonmail.com/
[P2QR-sipa]: https://delvingbitcoin.org/t/changes-to-bip-360-pay-to-quantum-resistant-hash-p2qrh/1811/20
[P2TRv2-bluematt]: https://gnusha.org/pi/bitcoindev/c2684826-6c93-419b-9a96-c0f0a791c9ac@mattcorallo.com/
[sipa-taproot]: https://delvingbitcoin.org/t/changes-to-bip-360-pay-to-quantum-resistant-hash-p2qrh/1811/11
[steven-p2mr]: https://delvingbitcoin.org/t/changes-to-bip-360-pay-to-quantum-resistant-hash-p2qrh/1811/9
[BIP360]: https://github.com/bitcoin/bips/blob/master/bip-0360.mediawiki
