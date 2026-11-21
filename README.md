# AkkoCloud NodeQuality test explained: can the London KVM-CN2-A1 mini hit 500Mbps, and is CNY¥299/year worth it? (Geekbench 5, fio, routes, real China bandwidth)

The NodeQuality report on AkkoCloud's London KVM-CN2-A1 mini covers a 1 vCPU, 512MB RAM, 10GB SSD box that sells for **CNY¥99 per quarter or CNY¥299 per year**, and the most useful thing in it is the gap between the spec sheet and the wire: the port is labelled 500Mbps, but the tester could not reach that peak on a single local TCP stream, and TCP back to China ran at roughly 100Mbps. What you are paying for is the route, which is genuinely premium in both directions (CMIN2 for China Mobile, CN2 GIA/CTG for Telecom and Unicom), plus a Geekbench 5 score of 413 single-core that is normal for a one-core slice. What the sales copy tends to leave out: the assigned address is a **US broadcast IP** rather than a UK one, IPv6 is offline, and the plan carries **no refund policy**.

Below is the full London lineup with recorded prices, what each benchmark number means in practice, and the one tier most readers should buy.

> **Quick pick:** start with the London KVM-CN2-A1 mini at CNY¥99 for one quarter, and only move to the CNY¥299 annual term once the route proves itself on your own traffic. It is the cheapest way to get CMIN2 in and CN2 GIA out of mainland China from a European location, and 512MB is enough for a proxy, a jump host or a static site. 👉 [Check the current London A1 mini price and stock](https://bit.ly/akkocloud)

## Every London CN2 GIA plan, side by side

The mini is one SKU in a long London lineup, and the tiers differ in more than memory. The 500Mbps port sits on the cheap A-series plans; the CNY¥300-and-up tiers drop to 300Mbps, and the 4-core plans sit at 400Mbps, trading port speed for monthly traffic.

| Plan (London) | vCPU / RAM / SSD | Traffic · port | Price | Order |
| --- | --- | --- | --- | --- |
| KVM-CN2-A1 mini | 1 / 512MB / 10GB | 500GB · 500Mbps | CNY¥99/quarter · CNY¥299/year | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-A1 | 1 / 768MB / 10GB | 600GB · 500Mbps | CNY¥60/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-A2 mini | 1 / 768MB / 15GB | 800GB · 500Mbps | CNY¥149/quarter | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-A2 | 1 / 768MB / 15GB | 1.0TB · 500Mbps | CNY¥90/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-B1 mini | 1 / 1GB / 20GB | 1.2TB · 500Mbps | CNY¥699/year | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-B1 | 1 / 2GB / 20GB | 1.5TB · 300Mbps | CNY¥130/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-B2 | 1 / 2GB / 30GB | 3.0TB · 300Mbps | CNY¥300/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-C1 | 1 / 2GB / 30GB | 4.0TB · 300Mbps | CNY¥460/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-D1 | 2 / 4GB / 40GB | 6.0TB · 400Mbps | CNY¥700/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-E1 | 4 / 8GB / 60GB | 9.0TB · 400Mbps | CNY¥1,050/month | [Order](https://bit.ly/akkocloud) |
| KVM-CN2-F1 | 4 / 8GB / 60GB | 12TB · 400Mbps | CNY¥1,400/month | [Order](https://bit.ly/akkocloud) |

Those figures were recorded by a third-party plan tracker in June 2026, and the mini's quarterly and annual prices match what the NodeQuality report states, which is a decent cross-check. It is still not a checkout page: stock, renewal price and the exact tier mix can change, so read the cart before paying. Windows images are noted only on the 4GB-and-up plans.

Two structural things worth noticing. Memory climbs much faster than CPU, so the B1 at CNY¥130/month gives you 2GB and 1.5TB of traffic but a slower 300Mbps port, while the B1 mini at CNY¥699/year gives 1GB and 1.2TB at 500Mbps for less money over twelve months. If you want throughput, the mini and A-series plans are better value than the monthly mid-tiers; the mid-tiers are for people who need RAM and traffic, not burst speed.

## What Geekbench 5 and fio say about the hardware

It is a small Intel slice with modest disk I/O: **Geekbench 5 scores 413 single-core and 413 multi-core**, SysBench single-thread returns 660, and fio mixed read/write lands at **44.07 MB/s read and 44.14 MB/s write on 4k blocks** (about 11,000 IOPS each).

The rest of the system readout from the 13 June 2025 test: Intel Core Processor (Broadwell) at 2,494MHz, one core, AES-NI and VM-x/AMD-V both enabled, 464.9 MiB usable of the nominal 512MB, 1.0 GiB swap, 11.0 GiB disk, Debian 12 on kernel 6.12.22, uptime 12 days. Single and multi-core Geekbench are identical because there is only one core to schedule.

Disk numbers break down like this:

- 64k blocks: 535.63 MB/s read, 538.45 MB/s write
- 512k blocks: 525.16 MB/s read, 553.06 MB/s write
- 1m blocks: 521.60 MB/s read, 556.34 MB/s write

The 4k figure is the one that governs your day. Sequential throughput above 500MB/s is fine for static files, logs and package installs, but 44MB/s of random writes is SATA-class, and it shows up as slow MySQL writes, slow `apt` operations on a busy box and painful swapping. On a 512MB plan with 1GB of swap, swapping is a real possibility, and it will happen on a disk that does 44MB/s at 4k.

One correction to the folklore: several roundups describe AkkoCloud's fleet as AMD EPYC 7002 with NVMe on higher tiers. This node reported Broadwell. Treat the CPU platform as a lottery and buy the plan for its route.

## Routes: the part you are actually paying for

Both directions are premium. Outbound runs Mobile via CMIN2, Unicom via 10099 and Telecom via CN2/CTG GIA; return traffic runs CMIN2 for Mobile and CTG/CN2 GIA for Telecom and Unicom. Symmetric premium routing is where budget "CN2" plans usually cut corners, so it is worth checking both directions whenever you compare providers.

The report's Beijing, Shanghai and Guangzhou route tables and multi-location ping charts are images, and no millisecond figures are printed in the text. Any latency number you see quoted for this box elsewhere is second-hand. Ping it from your own city before you commit to a year.

## The 500Mbps label and the ~100Mbps you can use

The iPerf3 conclusion in the report is blunt: **local single-thread TCP cannot reach the advertised 500Mbps peak, and TCP back to China is only around 100Mbps**. The TCP test ran against Guangzhou Mobile (500Mbps), Beijing Unicom (1Gbps), Ningbo Telecom (500Mbps) and a London endpoint (1Gbps); UDP single-thread covered three of those. Per-node figures are not published in the text, so there is no defensible way to say which path was fastest.

100Mbps is about 12.5MB/s. One person streaming 4K or pulling a nightly backup will not notice. Ten people pulling from the same box at once will. Single-thread matters more than usual here because proxy downloads are typically one TCP flow each, so the single-stream ceiling is closer to what an individual user feels than a multi-thread aggregate would be. If your plan involves serving large files to Chinese visitors, size the box against roughly 100Mbps, not 500.

👉 [See the current London plan list before you pick a tier](https://bit.ly/akkocloud)

## US broadcast IP, IPv6 offline

The IP quality line in the report reads "US broadcast IP". Lookup data puts the machine at Kirino LLC (AS41378), hosted by HK CEDOC Limited, geolocated in London, England. The server sits in London; the address block is American.

That rules out the most common reason people pick a London box: a UK-native address for UK streaming catalogues, UK banking or geo-testing. One widely repeated claim that the London location suits "UK native IP needs" does not survive the measurement on this plan. If a UK IP is non-negotiable, budget for a dedicated IP or a different product.

IPv6 is offline. If your stack assumes v6 (some monitoring targets, some CDN origin checks, some mail paths), plan around it rather than assuming you can enable it later.

## No refunds, and what that means for the first order

The report quotes the policy directly: no refunds, and if the merchant cannot provide service for its own reasons, the money returns to your account balance rather than your card. A plan tracker's notes on the London listing repeat the "refunds not allowed" line. Once the box is provisioned, the money is spent.

That single fact should decide your billing cycle. The mini costs CNY¥99 per quarter, so a full year bought quarterly is CNY¥396, against **CNY¥299 for the annual term**. You save CNY¥97, about a quarter, by committing upfront. Buying one quarter first costs you that CNY¥97 in exchange for an exit after 90 days instead of after 365. On a plan with no refund path, that is cheap insurance.

The comparison gets starker against the monthly plans: the CNY¥60/month A1 with 768MB runs CNY¥720 over a year, well over double the annual mini, for 256MB more RAM.

👉 [Start with a single quarter on the A1 mini](https://bit.ly/akkocloud)

## If you need more RAM, or a German node

Frankfurt runs a parallel ladder that starts at **CNY¥50/month** and climbs to roughly CNY¥1,400/month across eight tiers, with Windows images on the 4GB-and-up plans. Third-party listings disagree on the middle tiers by 15–20%, so read the cart rather than a blog table. For a pre-purchase latency check, the published Frankfurt test IP is 185.217.109.94.

Frankfurt makes sense when you need 1.5GB or more of RAM at a monthly commitment, or when your users are in Europe and you want a shorter physical path. London makes sense when you want the cheapest entry into the same China-optimised routing, and can live with 512MB.

👉 [Compare the Frankfurt and London tiers at checkout](https://bit.ly/akkocloud)

## Who should buy this, and who should not

Buy it if you want a China-optimised return path on a small budget, if you are running a single-user proxy or relay where one TCP flow at a time is the normal case, if you need a London jump host for European work with tolerable access from China, or if you want a static site or small blog behind a CDN. The 512MB plan handles a lightweight web stack as long as you keep the database off it, or keep it tiny.

Skip it if you need a UK-native IP, need IPv6, need a refund window or an SLA to point at, need more than about 1GB of RAM, need sustained 500Mbps, or need fast random disk writes for a database. Those are all legitimate needs; this plan just does not meet them, and no amount of route quality fixes a 44MB/s 4k write figure.

## Before you pay

Confirm two things on the order page rather than in a review: the price and stock for the exact tier you want, since the figures above are a mid-2026 snapshot of a lineup that has already shifted once, and the refund terms as they appear at checkout, because a no-refund policy with balance-only compensation is the whole reason to start quarterly.

If both check out and you want the cheapest credible entry into CMIN2 plus CN2 GIA from Europe, the A1 mini is the tier to test. 👉 [Check today's price for the London KVM-CN2-A1 mini](https://bit.ly/akkocloud)
