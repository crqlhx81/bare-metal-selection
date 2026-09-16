# host dedicated server: How to Pick a Bare Metal Box That Actually Fits Your Workload

If you typed "host dedicated server" into a search box, you're probably past the "what's a VPS" stage. You have a workload that's outgrown shared virtualization, or you have a compliance reason to need a single-tenant physical box, and now you're trying to figure out what actually matters when you buy one. This is the part most provider pages skip — they list specs, you compare numbers, and somewhere in between you realize you don't know whether you need 8 cores or 64, whether NVMe matters for your database, or whether the "premium network" line item is worth $200/month.

This guide walks through what a dedicated server actually buys you, where the real cost lives (hint: it's usually the network, not the box), and how to read a provider's spec sheet without getting pulled around by marketing. We'll use DMIT as a concrete reference point — their bare metal and cloud instance lineup is unusually transparent about the tradeoffs between hardware tiers and network tiers, which makes it a useful example even if you end up buying elsewhere.

## What "host dedicated server" actually means

A dedicated server — sometimes called bare metal — is a single physical machine rented to you alone. No hypervisor carving it into virtual machines for other tenants, no vCPU oversubscription where your noisy neighbor's cron job eats your IOPS at 3 AM. You get the whole box: CPU, RAM, disks, NIC, the lot.

That distinction matters more than the spec sheet suggests. On a VPS, even a "dedicated vCPU" plan shares the memory bus, the network card, and the storage controller with other tenants. On bare metal, the only thing between your workload and the silicon is the operating system you install. For most web workloads that's overkill. For databases that need consistent IOPS, virtualization hosts where you're running your own VMs, rendering, real-time game servers, and anything with strict isolation or compliance requirements, it's the difference between predictable performance and periodic mysterious slowdowns.

DMIT's bare metal page frames it cleanly: single-tenant, fully isolated hardware, full root and IPMI access, consistent predictable performance. That's the contract. Everything else — CPU model, RAM size, disk layout, network tier — is configuration on top of that contract.

## Where the real cost lives: the network, not the box

Here's the part most buyers underestimate. A mid-range AMD EPYC box with 64 cores, 128 GB RAM, and a couple of NVMe drives is, in 2026 hardware terms, a known quantity — plenty of providers will sell you one. The variable that actually drives price and quality is the network attached to it.

This is where DMIT is a useful reference, because they explicitly split their network into three tiers and tell you what each one is for. Most providers bundle "network" into a single line item and let you figure out the tradeoffs after you've already paid.

**Premium Network** combines Tier 1 transit with China Telecom's CN2 GIA and DMIT's own backbone. It's the tier for workloads where the end-user experience in mainland China and the wider Asia-Pacific region matters most — corporate sites, e-commerce, live streaming, real-time apps, cross-border payment platforms. You pay more per GB, and you get lower latency, fewer hops, and significantly reduced packet loss. In Hong Kong, DMIT reports roughly 15 ms average latency to China Mainland with packet loss under 0.1%. Plain Tier 1 transit doesn't hit those numbers.

**Eyeball Network** pairs Tier 1 transit with reasonable-effort China routing via CMIN2 and Chinese eyeball ISPs. It's the middle ground — noticeably better for Chinese residential users than raw Tier 1, noticeably cheaper than Premium. Good for mixed China/global audiences, API backends, download mirrors, SaaS platforms serving global users.

**Tier 1 Network** is the economical option — clean international capacity across APAC, North America, and Europe with no China-specific optimization. Best for backups, bulk transfers, internal tooling, CI/CD, VPN and relay nodes, and workloads where China routing isn't a factor.

The honest tradeoff, which DMIT states on their own page: premium China-optimized capacity is a finite, high-cost resource. You pay more per GB on Premium, and during network attacks or cost adjustments the non-premium tiers can see route changes. If guaranteed premium routing is non-negotiable, you stick with Premium-designated products and read the terms carefully. If your users aren't in China, paying for CN2 GIA is wasted money.

This three-tier framing is the right mental model for any dedicated server purchase, not just DMIT. Before you compare CPU specs, figure out which network tier your workload actually needs.

## DMIT bare metal: what's actually on offer

DMIT's bare metal page doesn't publish a fixed price list. Instead, it describes three configuration categories and asks you to request a quote. That's common for serious bare metal — the configurations are assembled to spec rather than pulled off a rack, and the price depends on your CPU, RAM, disk, bandwidth, and IP requirements.

| Configuration tier | Target workload | Hardware range | Network options | Pricing | Get a quote |
| --- | --- | --- | --- | --- | --- |
| Compute Optimized | CPU-bound workloads: databases, app servers, virtualization hosts | AMD EPYC up to 128 cores / 256 threads, DDR4/DDR5 ECC up to multi-TB, NVMe | Premium / Eyeball / Tier 1, custom port speeds | Custom quote | [Request Compute quote](https://bit.ly/DmiT) |
| Storage Optimized | Data-intensive workloads needing capacity and consistent low-latency IOPS | All-NVMe / SSD / large HDD arrays, hardware & software RAID | Premium / Eyeball / Tier 1, custom port speeds | Custom quote | [Request Storage quote](https://bit.ly/DmiT) |
| Enterprise & Custom | Special builds: GPU, large-memory, dedicated clusters | Custom CPU/RAM/disk, GPU & accelerators, IPMI included | Premium / Eyeball / Tier 1, BGP & BYOIP available | Custom quote | [Request Enterprise quote](https://bit.ly/DmiT) |

What you get on the hardware side, per DMIT's bare metal page:

- AMD EPYC platforms, up to 128 cores / 256 threads on the latest generation
- DDR4 / DDR5 ECC memory, scalable into the multi-terabyte range
- All-NVMe storage with optional RAID, plus HDD arrays for capacity-optimized builds
- 10Gbps uplinks with custom port speeds available
- Full root access and IPMI / out-of-band management for reinstall control
- GPU and accelerator options on request for the Enterprise tier

If you want a sense of the entry-level price floor before committing to a bare metal quote, DMIT's cloud instances on the same network give you a useful reference point. Bare metal sits well above those numbers because you're buying the whole physical box — but the network quality and data center footprint are identical.

## DMIT cloud instances: the full plan reference

If your workload doesn't actually need a whole physical box, DMIT's cloud instances run on the same AMD EPYC hardware and the same three network tiers, with self-service provisioning and free instant setup. The plans below are the ones currently shown on DMIT's location pages; prices are monthly, in USD, and DMIT notes they may be adjusted and are for reference only.

### Los Angeles — Premium Network (AS3 platform)

| Plan | vCore | RAM | Disk | Transfer | Port | Price/mo | Buy |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TINY | 1 | 2GB | 20GB SSD | 1000GB | 1Gbps | $10.90 | [Get LAX Premium TINY](https://bit.ly/DmiT) |
| Pocket | 2 | 2GB | 40GB SSD | 1500GB | 4Gbps | $16.90 | [Get LAX Premium Pocket](https://bit.ly/DmiT) |
| STARTER | 2 | 2GB | 80GB SSD | 3000GB | 10Gbps | $34.90 | [Get LAX Premium STARTER](https://bit.ly/DmiT) |
| MINI | 4 | 4GB | 80GB SSD | 5000GB | 10Gbps | $62.90 | [Get LAX Premium MINI](https://bit.ly/DmiT) |
| MICRO | 4 | 4GB | 160GB SSD | 7000GB | 10Gbps | $87.90 | [Get LAX Premium MICRO](https://bit.ly/DmiT) |
| MEDIUM | 6 | 8GB | 160GB SSD | 15000GB | 10Gbps | $199.90 | [Get LAX Premium MEDIUM](https://bit.ly/DmiT) |

A heads-up from DMIT: the LAX AS3 series is still being built out and optimized, so during this period you may experience reduced disk performance and a lower SLA than their mature platforms.

### Los Angeles — Premium Network (AN5 platform, curated selection)

| Plan | vCore | RAM | Disk | Transfer | Port | Price/mo | Buy |
| --- | --- | --- | --- | --- | --- | --- | --- |
| LAX.AN5.Pro.MINI | 4 | 4GB DDR4 | 80GB SSD | 5000GB | 10Gbps | $79.90 | [Get LAX AN5 MINI](https://bit.ly/DmiT) |
| LAX.AN5.Pro.MICRO | 4 | 4GB DDR4 | 160GB SSD | 7000GB | 10Gbps | $110.90 | [Get LAX AN5 MICRO](https://bit.ly/DmiT) |
| LAX.AN5.Pro.MEDIUM | 6 | 8GB DDR4 | 160GB SSD | 15000GB | 10Gbps | $289.90 | [Get LAX AN5 MEDIUM](https://bit.ly/DmiT) |

The AN5 platform runs AMD EPYC 9005 series (Zen 5) with DDR5 and PCIe 5.0 NVMe — DMIT's flagship for single-core and multi-core performance. The plans above are a curated selection of the most popular configurations; the full AN5 lineup is wider.

### Hong Kong — Premium Network (AN5 platform)

AN5 plans in Hong Kong are currently only offered on the Premium network.

| Plan | vCore | RAM | Disk | Transfer | Port | Price/mo | Buy |
| --- | --- | --- | --- | --- | --- | --- | --- |
| MINI | 4 | 4GB | 80GB SSD | 1500GB | 1Gbps | $149.90 | [Get HKG Premium MINI](https://bit.ly/DmiT) |
| MICRO | 4 | 4GB | 160GB SSD | 2000GB | 1Gbps | $199.90 | [Get HKG Premium MICRO](https://bit.ly/DmiT) |
| MEDIUM | 6 | 8GB | 160GB SSD | 2500GB | 1Gbps | $279.90 | [Get HKG Premium MEDIUM](https://bit.ly/DmiT) |
| LARGE | 8 | 16GB | 320GB SSD | 3000GB | 1Gbps | $359.90 | [Get HKG Premium LARGE](https://bit.ly/DmiT) |
| GIANT | 12 | 24GB | 640GB SSD | 6000GB | 1Gbps | $759.90 | [Get HKG Premium GIANT](https://bit.ly/DmiT) |

Hong Kong sits in Equinix HK2 at Kwai Chung — a primary carrier-neutral gateway to China, with dual CN2 GIA + CMI cross-border links. DMIT reports ~15 ms average latency to China Mainland with packet loss under 0.1%.

### Tokyo — Premium Network (AS3 platform)

| Plan | vCore | RAM | Disk | Transfer | Port | Price/mo | Buy |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TINY | 1 | 1GB | 20GB SSD | 500GB | 1Gbps | $21.90 | [Get TYO Premium TINY](https://bit.ly/DmiT) |
| STARTER | 1 | 2GB | 40GB SSD | 1000GB | 1Gbps | $45.90 | [Get TYO Premium STARTER](https://bit.ly/DmiT) |
| MINI | 2 | 4GB | 60GB SSD | 2000GB | 1Gbps | $89.90 | [Get TYO Premium MINI](https://bit.ly/DmiT) |
| MICRO | 4 | 4GB | 80GB SSD | 4000GB | 1Gbps | $189.90 | [Get TYO Premium MICRO](https://bit.ly/DmiT) |
| MEDIUM | 4 | 8GB | 160GB SSD | 6000GB | 1Gbps | $320.90 | [Get TYO Premium MEDIUM](https://bit.ly/DmiT) |
| LARGE | 8 | 16GB | 320GB SSD | 8000GB | 1Gbps | $429.90 | [Get TYO Premium LARGE](https://bit.ly/DmiT) |
| GIANT | 8 | 24GB | 640GB SSD | 15000GB | 1Gbps | $829.90 | [Get TYO Premium GIANT](https://bit.ly/DmiT) |

Tokyo lives in Equinix TY8 in Shinagawa, with ~28 ms average latency to China Mainland and 1.4 Tbps of Tier 1 transit. The AS3 platform here is AMD EPYC 7003 (Milan) — DMIT's value tier for Tokyo.

> Note on Tier 1 and Eyeball plans: DMIT also offers Eyeball and Tier 1 network series across LAX, HKG, and TYO at lower price points than the Premium plans shown above. The full matrix is too large to reproduce here — the LAX Eyeball page, for example, runs a separate promotion track — so if your workload doesn't need CN2 GIA, 👉 [check the full DMIT pricing page](https://bit.ly/DmiT) for the Eyeball and Tier 1 lineups in your preferred location.

## How to actually choose: a practical walkthrough

Most "how to choose a dedicated server" guides list five generic factors and call it a day. The version that actually helps goes in a different order.

**1. Figure out your network tier before your CPU.** This is the step most people skip and it's the most expensive one to get wrong. If your users are in mainland China and your workload is latency-sensitive (payments, live streaming, real-time apps), you need CN2 GIA or equivalent premium routing — anything else will collapse during evening peak hours. If your users are global and China is incidental, Tier 1 is fine and you're wasting money on Premium. If you're not sure, start on Eyeball and upgrade if you see the latency spike.

**2. Pick hardware that matches the workload, not the spec sheet.** A busy PostgreSQL box wants high single-core clock speed and fast NVMe — a 32-core chip at 2.0 GHz is worse than a 16-core chip at 3.5 GHz. A virtualization host wants cores and RAM, and disk speed barely matters. A storage-heavy workload wants capacity and RAID, not the fastest NVMe on the market. DMIT's three bare metal tiers (Compute, Storage, Enterprise) map roughly to this logic — use them as a template even if you're shopping elsewhere.

**3. Decide whether you actually need bare metal.** If your workload runs fine on a 4 vCore / 8 GB VPS, buying a 64-core bare metal box is a waste. The honest test: have you hit a performance ceiling that's clearly the hypervisor's fault (noisy-neighbor IOPS, CPU steal, memory bus contention), or a compliance requirement that mandates single-tenant hardware? If neither, a high-tier cloud instance on the same network is usually the better deal. DMIT's LAX Premium MEDIUM at $199.90/month for 6 vCore / 8 GB / 15 TB transfer is a serious machine for most workloads — bare metal only makes sense when you've outgrown that.

**4. Read the SLA and the refund policy before you commit.** DMIT publishes a 99% uptime SLA with real teeth: drop below 99% and you get half a month credited, below 95% a full month, below 90% two months. They also offer a 3-day money-back guarantee (up to 30 GB transfer) and a 30-day prorated refund policy on most services. That's better than most. Whatever provider you pick, get the SLA and refund terms in writing before you pay — "we'll try our best" is not an SLA.

**5. Ask about IP resources up front.** If you need additional IPv4 blocks, IPv6 allocations, BGP sessions, or BYOIP announcements, this is a separate conversation from the box itself. DMIT lists all of these as available on their bare metal page; some providers don't offer BGP at all, and some charge per-IP per-month in ways that materially change the bill.

## Current DMIT promotions (verify at checkout)

DMIT releases discount codes irregularly, usually tied to product launches or seasonal promotions, and most codes only activate on quarterly or longer billing cycles — monthly billing rarely qualifies. The codes below have been circulating; verify them at checkout since promotional periods vary.

- **LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF** — 20% recurring discount on LAX Eyeball TINY series or higher, quarterly payment and above. Confirmed on DMIT's LAX Eyeball page.
- **HKG-T1-ANNUALLY-45OFF-RECUR** — 45% lifetime discount on Hong Kong Tier 1 annual plans, plus upgraded specs (more vCPU, double disk, 50%+ memory, higher IO). Reported in 2026 community tracking.
- **2025-TYO-T1-HI-GSL-NON-MONTHLY-30OFF** — 30% lifetime discount on Tokyo Tier 1 quarterly or annual billing.
- **SJC-Unmetered-Annually-30OFF** — 30% off annually paid San Jose unmetered plans.

A couple of important caveats: DMIT's terms explicitly state that discount codes only apply to new customers, and using a code issued to a specific existing user can get your service suspended without refund. If you're provisioning a bare metal server, ask the sales team directly whether any of these codes apply — bare metal is quote-based, so promotions aren't always advertised the same way they are for VPS.

👉 [View latest DMIT official promotions](https://bit.ly/DmiT)

## Who should actually buy a DMIT dedicated server

After reading through the configs, the network tiers, and the user feedback, the fit is fairly clear.

**Strong fit:**

- Cross-border e-commerce and finance platforms serving mainland China users, where peak-hour latency directly costs revenue
- Game servers and real-time interactive applications that need single-digit-millisecond stability into China
- Workloads with strict isolation or compliance needs — single-tenant hardware, ISO 27001 / SOC 2 / PCI DSS facilities
- Teams that have been burned by oversold VPS providers and want predictable, dedicated hardware
- High-traffic streaming and media delivery where CN2 GIA routing materially improves the viewer experience

**Probably overkill:**

- Hobby projects and personal blogs where a $5 VPS is fine
- Pure backup and archival workloads with no China-facing component (DMIT's Tier 1 cloud instances are cheaper than bare metal here)
- Anything where you need fully managed support — DMIT is mostly unmanaged, with a 72-hour ticket SLA

If you land in the "strong fit" column, the next step is straightforward: tell DMIT your workload, your traffic profile, and your preferred location, and their team assembles a tailored bare metal configuration and quote. That's how their bare metal sales actually work — no off-the-rack pricing page, just a conversation about what you need.

👉 [Build your DMIT bare metal server](https://bit.ly/DmiT)

## A note on the LAX AS3 buildout

DMIT flags on their own pricing page that the LAX AS3 series is still being built out and optimized, and during this period you may experience reduced disk performance and a lower SLA than their mature platforms. If you're buying LAX Premium on the AS3 platform specifically, ask the sales team about the current state before you commit — the AN5 platform in LAX is the mature flagship, and the price difference between AS3 and AN5 on the same plan size is small enough that for production workloads the AN5 route is usually the safer pick.

## The bottom line

A dedicated server is a commitment, both in money and in operational responsibility. The box itself is a known quantity in 2026 — AMD EPYC, NVMe, DDR5, 10Gbps uplinks are table stakes. The variable that actually differentiates providers is the network attached to that box, and that's where DMIT's three-tier framing (Premium / Eyeball / Tier 1) is genuinely useful even if you end up buying from someone else. Figure out your network tier first, your hardware second, and your provider third. If your users are in China and your workload can't tolerate a hypervisor or a congested gateway, DMIT's pitch — CN2 GIA, direct peering with all three Chinese carriers, Tier IV facilities in LA, HKG, and TYO — is one of the few that holds up under scrutiny. Whether it holds up for your specific budget is a conversation worth having with their sales team.
