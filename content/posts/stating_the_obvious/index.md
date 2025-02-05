+++
date = 2025-02-05
draft = false
type = "blog"
title = "Stating the obvious"
description = "A proposition of a scope for the Bitcoin Core project"
weight = 10
author = "Antoine Poinsot"
tags = ["Bitcoin", "Bitcoin Core"]
+++

Following [my post](https://antoinep.com/posts/core_project_direction) from a couple weeks ago, i
set out to answer my own question.

How do we define a project scope? An interesting exercise is to consider what should be the bounds if
we had near infinite resources. Should there be a limit in the scope of the project then? Surely you
can think of examples of features we'd never want to include in Bitcoin Core (for instance a general
purpose text editor, a browser, a recursive DNS resolver, ..).  But then where do we draw the line?
It's tempting to say "at non-Bitcoin stuff", but this is only begging the question. A message
encryption feature (or even `signmessage` for this matter) can be argued to be Bitcoin-related as
much as a DNS resolver.

I think in this case the scope should be "whatever is demanded by users, subject to veto by
contributors". The first part is simply because what is useful to Bitcoin Core users may vary so
widely across usage types and time that it's hard to draw a non-arbitrary line. The second part is
to reflect that developers won't implement features they believe to be harmful or dumb despite there
being user demand (think colored coins, inscriptions, altcoin client, ..). Near infinite resources
means we can assume only those users interested in a feature set bear its associated costs (larger
binary, additional dependencies, etc). Of course it also means that implementing, reviewing and
maintaining a new feature does not materially affect the ability of preserving the quality of the
rest of the software.

Unfortunately in real life we do not have near infinite resources. Work on a new feature necessarily
presents an opportunity cost: this development and review time could have been spent elsewhere. In
addition it presents future costs: it will have to be maintained, and may interact with new features
being introduced later on. Future work on test infratructure, modernizing the codebase, etc.. now
will have to deal with it. This is *not* an abstract concern. It is also made worse by the
incentives surrounding the contribution dynamic. Absent a project scope, any feature that receives
enough review could make it in. The authors get the benefit of getting their feature merged into
Bitcoin Core, and the maintainers and future contributors pay the associated costs.

These costs mean we need to make tradeoffs. We cannot both keep piling more code, more features AND
also maintain a high bar for quality. High bar for quality meaning the code we ship received
significant scrutiny, testing and that we are actively working to find bugs (especially
[security-critical ones](https://bitcoincore.org/en/security-advisories)). Does this mean we should
allocate all our resources toward improving testing and review? Absolutely not! Some developments
are well worth the risk and maintenance cost. And software costs/risks need to be balanced against
network and industry wide costs, risks and opportunities. That is, a Bitcoin Core vs Bitcoin
tradeoff. For instance it's worth it for Bitcoin Core to bear the complexity introduced by [package
relay](https://github.com/bitcoin/bitcoin/issues/27463) because it significantly improves the
robustness of second layer protocols used by a large number of Bitcoin users. Another example is
compact block relay. Does [`CVE-2024-35202`](https://bitcoincore.org/en/2024/10/08/disclose-blocktxn-crash/)
means Bitcoin Core should not have implemented this protocol upgrade? Absolutely not! Implementing
mitigations against fundamental centralization pressures in Bitcoin literally is at the core of
Bitcoin Core's mission.

But what is Bitcoin Core's mission? It seems obvious to say that block relay should be a priority
for the Bitcoin Core project. But at what cost? And how do you generalize that? Here again, the
answer is the vague truism "Bitcoin Core should prioritize in function of what its users value
most". But different users are going to value different things. Some will prefer to have new indexes
to query mempool data, others will want a more efficient wallet that support higher loads, and
others yet would prefer to have Assumeutxo implemented in the GUI. And from this it's easy to shrug
and conclude "there is no good way to prioritize one type of user over another, people will just
work on what they want". But this is sticking the head in the sand to avoid seeing the issue and
this is missing a critical distinction in the group of Bitcoin Core users.

For technical and historical reasons, Bitcoin Core is the only reasonable full node implementation
for all Bitcoin users to run. Re-implementations of Bitcoin face significant technical challenges
and appear to be doomed to have consensus bugs. Forks of Bitcoin Core, while more reasonable to run
from a consensus bugs perspective, have nowhere near the level of quality assurance Bitcoin Core
has. But Bitcoin Core does not hold a monopoly, and its advantage can erode. A large share of users
turning to other implementations would be a major setback for the project, and, given the state of
available alternatives at the moment, one for the industry at large too.

When you consider that Bitcoin Core's userbase is indirectly all Bitcoin users, how to prioritise
development becomes quite clear. Bitcoin Core should be a robust backbone for the Bitcoin network,
balancing between securing the Bitcoin Core software and implementing new features to strengthen and
improve the Bitcoin network. I know this feels like stating the obvious, but sometimes it appears to
be necessary.
