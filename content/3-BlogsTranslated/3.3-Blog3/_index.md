---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Private NAT Gateway - How United Airlines Solved IP Exhaustion When Systems Need to Scale Rapidly

In large-scale cloud systems, sometimes the issue isn't a lack of compute, containers, or serverless capacity, but rather something very fundamental: **IP addresses**.

**United Airlines** is a major airline in the US, operating systems that serve hundreds of millions of passengers annually. When incidents occur—such as bad weather, mass flight cancellations, or disruptions due to air traffic control issues—United's systems need to scale rapidly to handle many tasks simultaneously: rebooking flights, updating itineraries, processing flight data, managing baggage, and reassigning crew members.

However, in a **hybrid network environment** with hundreds of AWS accounts connected to on-premises systems, United faced a difficult constraint: **IP address exhaustion for routable IPv4 addresses**.

---

## The Problem: Compute Can Scale, But IPs Cannot

In AWS, many workloads running within a VPC need **Elastic Network Interfaces (ENI)**. For example:

- Amazon ECS tasks.
- AWS Glue jobs.
- AWS Lambda functions attached to a VPC.

Each ENI consumes an IP address within a subnet. For large enterprises like United Airlines, private **RFC 1918** IP ranges are typically allocated very tightly to avoid conflicts with on-premises systems, other VPCs, or internal networks.

Under normal conditions, the allocated IPs may be sufficient. But when widespread incidents occur, many workloads scale up simultaneously. ECS tasks increase, Glue jobs run more frequently, Lambda functions fan-out massively. All drawing from a limited IP pool.

The result is that the system can hit the IP ceiling exactly when it needs to expand most.

The challenge is that requesting additional routable IP ranges cannot be done in minutes. It requires coordination with the network team, updating routing, firewall rules, and checking for overlaps. In a large environment, this process can take weeks.

United needed a way for compute to scale rapidly without depending on available routable IP addresses.

---

## Why Not Just Request More IPs or Switch to IPv6?

Before choosing **Private NAT Gateway**, United considered many alternatives.

**Requesting additional RFC 1918 space** was the most direct approach, but unsuitable for sudden scaling needs due to lengthy coordination time.

**IPv6** is a long-term solution to IPv4 exhaustion. However, with an enterprise having hundreds of accounts, routing systems, monitoring, and security policies built on IPv4, switching to IPv6 would be a major organizational decision that cannot be implemented quickly just to solve a short-term problem.

**AWS PrivateLink** works when exposing specific services between VPCs, but United's workloads need outbound connectivity to many places: Transit Gateway, on-premises systems, shared services, and multiple VPCs.

**Amazon VPC Lattice** fits a clear service-to-service model, but United's problem leans toward general outbound connectivity—workloads need to reach many different destinations rather than just calling pre-registered services.

**Network renumbering** could solve it fundamentally, but at this scale, it's a multi-year project.

Therefore, United chose Private NAT Gateway because it can be deployed in weeks, requires no changes to destination systems, and works with existing IPv4 infrastructure.

---

## The Core Idea of the Solution

Instead of placing all workloads in subnets using scarce routable IPs, United moves compute workloads to the `100.64.0.0/10` range.

This is the **Carrier-Grade NAT** range per **RFC 6598**. These addresses are not routed within United's corporate network, so they can be used for compute subnets without requesting allocation from the traditional routable IPv4 pool.

But there's a problem: workloads running on the `100.64.x.x` range cannot directly communicate outside the VPC—for example, to on-premises systems, shared services, or other VPCs via Transit Gateway.

This is where **Private NAT Gateway** comes into play.

Private NAT Gateway is placed in a routable subnet. When workloads from the `100.64.x.x` subnet send traffic outbound, Private NAT Gateway translates the source IP from the non-routable address to the gateway's routable address. Traffic can then proceed via Transit Gateway to target systems.

Simply put:

- Compute uses non-routable IPs to avoid address exhaustion.
- Private NAT Gateway translates traffic to routable IPs.
- Destination systems need no changes.

---

## How Does the Architecture Work?

United's model can be understood in three main parts.

First is **non-routable compute subnets** using the `100.64.0.0/10` range. This is where ECS tasks, AWS Glue jobs, and VPC-attached Lambda functions run.

Second are **routable subnets**. These still use IP ranges routable within the corporate network. Private NAT Gateway, load balancers, or components needing direct access sit here.

Third is the **Transit Gateway** connecting to other VPCs, shared services, and on-premises systems.

Traffic flows as follows:

1. Workload in the `100.64.x.x` subnet sends a request outbound from the VPC.
2. The route table of the non-routable subnet directs default traffic to Private NAT Gateway.
3. Private NAT Gateway translates the source IP to the gateway's routable IP.
4. Traffic proceeds via Transit Gateway to the destination system.
5. Response returns to Private NAT Gateway.
6. Gateway translates back to the workload's original IP.

Workloads inside don't need to know NAT is happening. They send requests normally.

---

## How Are Route Tables Configured?

Basically, United uses two types of route tables.

For **non-routable subnets**, internal VPC traffic still goes local. Services like S3 or DynamoDB can go through VPC endpoints. Other traffic goes via Private NAT Gateway.

For **routable subnets**, default traffic can go via Transit Gateway to reach corporate networks, on-premises systems, or other VPCs.

This configuration clearly separates roles:

- Non-routable subnets for large-scale compute.
- Routable subnets for components needing broader network connectivity.
- Private NAT Gateway as the address translation layer between zones.

---

## Results Achieved

After using Private NAT Gateway, United Airlines can decouple compute scaling from routable IP constraints.

Previously, during **IRROPS**, tasks or functions could fail due to subnet IP exhaustion. After switching to this model, compute workloads can scale on non-routable subnets without being limited by scarce routable IP pools.

Capacity addition time changed significantly. Instead of waiting weeks for IP requests and firewall updates, teams can expand workloads in minutes on the `100.64.0.0/10` range.

This is especially critical for aviation—a single incident preventing the rebooking system from scaling quickly could impact tens of thousands of passengers in short timeframes.

---

## Key Lessons from Operations

Some points warrant attention when deploying Private NAT Gateway at scale.

First, deploy NAT Gateways appropriately per **Availability Zone**. With heavy traffic, gateways experience high load. While a gateway has very high limits, thousands of short-lived Lambda invocations calling one endpoint could still cause **port exhaustion**. Monitor metrics like `ErrorPortAllocation` on CloudWatch.

Second, not all resources should move to non-routable subnets. Components needing direct corporate network access—like load balancers, NAT Gateways, or special interfaces—should remain in routable subnets.

Third, NAT hides the workload's original IP. Destination systems see the source as Private NAT Gateway rather than the container or Lambda IP. For tracing, use **VPC Flow Logs** with custom format to record both pre-NAT and post-NAT addresses.

Fourth, with **Amazon EKS**, United can use custom networking for pods to receive non-routable IPs. But with **ECS**, **Glue**, and **Lambda**, these services attach ENIs directly to the configured subnet, making Private NAT Gateway more appropriate for handling outbound connectivity.

---

## When to Use This Model?

Private NAT Gateway suits enterprises facing IPv4 exhaustion in hybrid networks, especially when:

- Multiple AWS accounts connect to on-premises systems.
- RFC 1918 space allocation is limited.
- Serverless or container workloads need sudden scaling.
- Requesting additional IPs takes significant time.
- Systems still heavily depend on IPv4.
- Outbound connectivity is needed to many VPCs, shared services, or on-premises systems.

This isn't a complete replacement for IPv6 long-term, but a practical way to handle scaling within current infrastructure.

---

## Conclusion

United Airlines' story illustrates a very real cloud networking issue: sometimes systems aren't limited by compute, but by IP addresses.

By moving workloads to non-routable `100.64.0.0/10` subnets and using Private NAT Gateway to translate traffic to routable networks, United removed the IP limit from scaling. Workloads like ECS, AWS Glue, and Lambda can expand faster during high-pressure periods without waiting for IP allocation from the network team.

In my view, the most valuable aspect is practicality. It requires no major destination system changes, doesn't need years of network re-architecture, and works with existing IPv4 infrastructure.

For large organizations facing IP constraints in hybrid AWS environments, Private NAT Gateway is a worthy approach to consider.

---

## Reference Links

[https://aws.amazon.com/blogs/networking-and-content-delivery/how-united-airlines-solved-ip-exhaustion-with-private-nat-gateway/](https://aws.amazon.com/blogs/networking-and-content-delivery/how-united-airlines-solved-ip-exhaustion-with-private-nat-gateway/)

---

![Blog 3](/images/Blog3-1.png)
![Blog 3](/images/Blog3-2.png)
![Blog 3](/images/Blog3-3.png)