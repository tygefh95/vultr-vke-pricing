# Cheap managed kubernetes: is the control plane free, what does a 3-node cluster actually cost, and who bills per node vs per cluster? (Vultr VKE pricing, $300 credit terms, and a comparison table)

A managed Kubernetes bill has three parts: the control plane, the worker nodes, and the add-ons (load balancer, block storage, egress). Providers advertise whichever part makes them look cheapest, so a $12/mo headline and a $60/mo headline can describe completely different things. On Vultr, the first part costs nothing: Vultr's own documentation states the Vultr Kubernetes Engine (VKE) control plane is fully managed at no additional cost, and you pay only for the Cloud Compute instances, load balancers and volumes you attach. New accounts can also claim $300 in promotional credit, which covers Kubernetes but expires about 30 days after activation.

Here is what a small cluster costs on that structure: two to three entry-level Cloud Compute nodes run roughly $5–6 per month each, so a test cluster lands in the low tens of dollars per month rather than the low hundreds. The optional HA control plane is the one line item worth confirming at checkout, because Vultr's cost documentation covers only the standard free control plane.

> **Most readers comparing on price:** VKE with 2–3 entry Cloud Compute nodes. Control plane $0, node sizes chosen at Cloud Compute rates, HA optional. The $300 new-user credit (code **FLY300VULTR**) applies to Kubernetes among other products, is limited to new customers, and expires roughly 30 days after it lands in your account, so treat it as a one-month test budget, not a year of free hosting.
> 👉 [Check current Vultr VKE pricing and the $300 new-user credit](https://bit.ly/VuLtr)

## What Vultr VKE actually bills you for

VKE doesn't sell cluster tiers the way some providers do. There is no "Starter cluster" SKU with a fixed monthly number, which is why you won't find a tidy plan grid on the pricing page. The cost is a sum of parts, and the parts are billed independently.

| Component | Billed? | Verified detail |
| --- | --- | --- |
| Control plane (standard) | No | Vultr documents a fully managed control plane at no additional cost, with automatic upgrades |
| HA control plane | Listed at $40/mo | Appears in third-party price tracking rather than Vultr's own cost doc, so confirm on the order screen before relying on it |
| Worker nodes | Yes, hourly or monthly | Any Cloud Compute instance with 2 GB RAM or more, at that instance's normal rate |
| Load balancers | Yes | Vultr Load Balancers billed separately from the cluster |
| Block storage | Yes | Volumes billed separately |

Two practical consequences follow. First, your cluster's monthly cost scales with node count and node size, not with a cluster fee, so a three-node development cluster and a thirty-node production cluster differ only in the node line. Second, because nodes are ordinary Cloud Compute instances, you can resize a pool by swapping instance types rather than moving to a different Kubernetes product.

## The $300 credit: what it covers and when it disappears

The offer circulating as the "$300 code" is **FLY300VULTR**: $300 in credit, new users only, roughly 30 days of validity. Vultr's published terms for promotional credit are specific in three ways that matter. It applies to select products, and the compute lineup including Cloud Compute, High Frequency, Optimized Cloud Compute, Cloud GPU, Bare Metal, Kubernetes and Managed Databases is covered. It cannot be combined with other offers, which means you choose between the $300 credit and the deposit-match offer that matches your first payment up to $100. And it is restricted to customers with no prior Vultr account.

The expiry is the part people miss. A user who tracked their own billing reported the credit landing on April 16 and the unused balance expiring on May 17. Nothing carries over, so the $300 is a fixed window rather than a bank balance. A $6/month instance will not consume $300 in 30 days no matter how long you leave it running; you would need to run several instances in parallel, or use GPU hours, to approach the full amount. Size the test to what you actually want to learn, and tear down what you're not using, because idle hourly instances burn credit that won't roll over.

Two more conditions from the signup flow: a card is required for verification before the credit activates, and you should confirm the credit balance and its expiry date in the billing section immediately after signup rather than assuming it attached.

👉 [Start a Vultr account and confirm the current new-user credit terms](https://bit.ly/VuLtr)

## The four ways providers quote a "cheap" managed Kubernetes price

This is where most comparisons go wrong. The numbers below are all real, all current as published, and none of them measure the same thing.

| Provider | What the headline covers | Verified figures | Constraint to note |
| --- | --- | --- | --- |
| Vultr (VKE) | Free control plane, nodes at Cloud Compute rates | Control plane $0; HA control plane listed at $40/mo; nodes from entry Cloud Compute tiers | Total depends entirely on node count and size |
| DigitalOcean (DOKS) | Page headline "Starting at $12/mo"; the control plane's share of that figure isn't stated in the page copy | 99.95% uptime SLA for the HA control plane, 99.99% for nodes and block storage | Can't tell from the headline whether $12 includes control plane |
| UpCloud (UKS) | Control plane priced separately, nodes at Cloud Server rates | Production $60/mo (multiple control plane hosts, up to 120 nodes); Development free (single control plane host, no HA, up to 30 nodes) | The free tier has no high availability |
| NexNodo | Per node, HA control plane included | From ~$17.52/mo per node (2 vCPU / 4 GB / 80 GB SSD / 4 TB); 3× Starter ≈ $52.56/mo; 99.5% Kubernetes SLA, 99.99% infrastructure SLA | Belgrade, Serbia only; launch pricing ends December 31, 2026, standard pricing from January 1, 2027 |
| RareCloud | Per node, priced in euros | Listed from €7.99/mo for one G-1 node; 3× G-2 at €44.97/mo; with HA control plane €74.97/mo | Listed page figures in EUR, not independently verified, so treat as a starting price only |

### Why you can't subtract these numbers

Take the two closest-looking figures: DigitalOcean's $12/mo and NexNodo's $17.52 per node. The second is more expensive per unit and includes an HA control plane at no separate fee. The first may or may not include control plane capacity, and the page copy doesn't say. Comparing them directly produces a conclusion the underlying data doesn't support.

The same problem applies to UpCloud. Its $60/mo Production tier buys you multiple control plane hosts and capacity for up to 120 nodes, but the worker nodes are billed on top at normal Cloud Server rates. A three-node cluster on UpCloud Production is $60 plus three Cloud Servers, not $60. Meanwhile the free Development tier is genuinely $0 for the control plane, with a single control plane host and a recommended ceiling of 30 nodes, which is fine for staging and a poor fit for anything that needs control plane redundancy.

RareCloud's numbers are in euros and come from a listing rather than a full page read, so they indicate that lower per-node pricing exists without supporting a precise cross-currency comparison. No exchange rate is applied here on purpose.

## Three costs that never make it into the headline

**Which layer the SLA covers.** Uptime percentages get quoted as if they describe the whole service. NexNodo splits its commitments deliberately: 99.5% for the Kubernetes platform layer, 99.99% for the underlying infrastructure. DigitalOcean commits to 99.95% for the HA control plane and 99.99% for nodes. Canonical advertises 99.9% guaranteed uptime with an enterprise SLA and upgrades included, and publishes no price at all beyond a "flexible pricing model", which means a quote rather than a checkout page. When you compare 99.9% against 99.95%, check what object each number attaches to, because a control plane SLA and a node SLA are not interchangeable.

**Region availability.** NexNodo's $17.52 per node is real and its HA control plane is genuinely free, but the service is available in Belgrade, Serbia only, with additional regions described as coming as the network expands. UpCloud offers both of its control plane tiers in all locations. If your users are in Frankfurt or Virginia, a single Southeast European region is a latency and redundancy decision, not just a price decision.

**Hourly billing without a minimum term.** NexNodo bills worker nodes hourly with no minimum term, and Vultr bills on-demand hourly with no long-term commitment. This cuts both ways. You can delete a cluster the same day you create it, and you can also leave one running for six weeks without noticing. Hourly billing rewards people who tear down test environments and quietly punishes people who don't.

## When the cheapest option is the wrong one

The free-control-plane, small-node configuration is the right default for a first production cluster, a staging environment, or a team migrating off a bill that got out of hand. It is the wrong choice in four situations.

You need genuine multi-zone resilience. A single region, or a single control plane host on a free tier, gives you no redundancy if that zone has a bad day. UpCloud's $60/mo Production tier and DigitalOcean's HA control plane exist for this reason, and the premium buys redundancy rather than performance.

You need GPU inference inside the cluster. DigitalOcean documents GPU-powered node pools aimed at model serving, and Vultr sells Cloud GPU by the hour, which the promotional credit covers. Running inference on CPU nodes to save $40 is a false economy once latency matters.

You manage multiple clusters. DOKS has been extending multi-cluster management, and if you're operating several clusters across environments, the tooling layer matters more than the per-node delta.

You need a contract with a name on it. Canonical's model, with an enterprise SLA, optional training and the ability to take back control of the cluster, is built for organizations that need a supplier relationship rather than a credit card form.

## FAQ

**Is a managed Kubernetes control plane free?**
It depends on the provider, and "free" usually has a qualifier. Vultr's standard VKE control plane carries no charge. UpCloud's Development tier is free but runs a single control plane host without HA, while its Production tier is $60/mo. NexNodo includes an HA control plane at no separate fee. DigitalOcean's page does not break out the control plane from its $12/mo starting figure.

**What's the cheapest way to run a small Kubernetes cluster?**
Keep the control plane free and keep the node count at the minimum your workload tolerates. On Vultr that means the standard control plane at $0 and two or three entry-level Cloud Compute instances, with the $300 new-user credit covering roughly the first month. On NexNodo it means three Starter nodes at about $52.56/mo with HA included, in Belgrade. Both are legitimate answers; they differ on region and on how long the price holds.

**Do I pay for the control plane or the nodes?**
On most entry-level tiers you pay for nodes and the control plane is bundled or free. On UpCloud Production you pay for both, $60/mo for the control plane plus Cloud Server rates for every worker. Read the pricing table for the phrase "charged by the worker node" or its equivalent before you commit.

**Does the $300 Vultr credit cover Kubernetes?**
Vultr's promotional credit applies to select products, and Kubernetes is among the covered services, alongside Cloud Compute, Cloud GPU and Managed Databases. The limits that matter are the ones on eligibility: new customers only, no stacking with the deposit-match offer, and roughly 30 days before the unused balance expires.

## How to decide

If you want the lowest realistic entry cost with a free control plane and no cluster fee, run VKE on two or three entry Cloud Compute nodes and use the $300 credit to cover the first month of testing. If you need a multi-control-plane production tier priced transparently per month, UpCloud's $60 Production tier is the clearer line item. If you want HA bundled into a per-node price and Belgrade is an acceptable region, NexNodo's launch pricing is worth costing out before December 31, 2026, since standard pricing applies from January 1, 2027.

Before you order anywhere, confirm three things on the order screen: the expiry date on any promotional credit, the region your nodes will actually run in, and whether the HA control plane is included or billed as an add-on. Those three answers explain most of the gap between a quoted price and a real invoice.

👉 [Compare Vultr VKE configurations and claim the $300 new-user credit](https://bit.ly/VuLtr)
