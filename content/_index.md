## About

I work on Bitcoin at [Chaincode Labs][], mostly on security-related topics. You can follow my recent
work on [GitHub][], [Delving Bitcoin][] and the [bitcoindev mailing list][].

I've done some security research in the Bitcoin space, and I'm involved in handling vulnerability
reports to Bitcoin Core. More recently, I've started engaging with how Bitcoin should
[prepare][quantum post] for a potential cryptographically-relevant quantum computer.

I've also worked on Bitcoin's scripting capabilities. I contributed to [Miniscript][] to make it
possible to safely use existing primitives to their full extent, and to [BIP 448][] to introduce new
useful primitives. These can make personal and professional self-custody safer, and help scale
Bitcoin by supporting offchain usage with different tradeoffs than existing solutions.

I previously co-founded [Wizardsardine][], where I remain a shareholder. There I researched Bitcoin
vaults and implemented [Revault][], a Bitcoin vault for multi-party custody. I also started and
maintained the popular [Liana][] wallet, which aims to reduce the risk of losing access to funds.

Before that I co-founded Bitcoin-Lyon, a local Bitcoin broker in Lyon, France.

I'm interested in information security, economics and politics.

## Publications

- [BIP 448][] - Bitcoin soft fork proposal to extend scripting capabilities with simple,
  well-understood primitives that improve offchain scaling protocols.
- [CVE-2025-62860][CVE-whitelist] - thousands of Bitcoin nodes had their DoS and privacy protections
  disabled. Companion blog post [here][CVE-whitelist-post].
- [CVE-2025-46598][] - Bitcoin Core could be repeatedly stalled for multiple seconds at virtually
  no cost to an attacker. The same setup cost could be reused to stall multiple nodes in parallel.
- [BIP 54][] - Bitcoin soft fork proposal to address four long-standing vulnerabilities and
  weaknesses.
- [CVE-2024-38365](https://delvingbitcoin.org/t/cve-2024-38365-public-disclosure-btcd-findanddelete-bug/1184?u=antoinep) - chain
  split vulnerability in btcd which can be exploited at virtually no cost.
- [Ledger Security Bulletin 019](https://wizardsardine.com/blog/ledger-vulnerability-disclosure) -
  some spending conditions of a Bitcoin Script were not correctly implemented by the Ledger firmware
  and could have led to a loss of funds.
- [CVE-2022-32984](https://blog.btcpayserver.org/btcpay-server-cve-2022-32984) - under some
  configurations the credentials to the Lightning node of a BTCPay Server point of sale were leaked,
  exposing all its funds to an attacker.
- [Swambo, J., Poinsot, A. (2021). Risk Framework for Bitcoin Custody Operation with the Revault
  Protocol.](https://arxiv.org/abs/2102.09392v2) - In: Bernhard, M., et al. Financial Cryptography
  and Data Security. FC 2021 International Workshops.

[BIP 54]: https://github.com/bitcoin/bips/blob/master/bip-0054.md
[CVE-whitelist]: https://bitcoincore.org/en/2026/11/10/disclose-cve-2025-62860/
[CVE-whitelist-post]: ./posts/cve-2025-62860/
[CVE-2025-46598]: https://bitcoincore.org/en/2025/10/24/disclose-cve-2025-46598/
[BIP 448]: https://github.com/bitcoin/bips/blob/master/bip-0448.md
[Chaincode Labs]: https://chaincode.com/about
[quantum post]: ./posts/quantum_risk_mitigation/
[Miniscript]: https://github.com/bitcoin/bips/blob/master/bip-0379.md
[Wizardsardine]: https://wizardsardine.com
[Revault]: https://wizardsardine.com/revault
[Liana]: https://wizardsardine.com/liana
[GitHub]: https://github.com/darosior
[Delving Bitcoin]: https://delvingbitcoin.org/u/antoinep/activity
[bitcoindev mailing list]: https://gnusha.org/pi/bitcoindev/?q=Antoine+Poinsot
