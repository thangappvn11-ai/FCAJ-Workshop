---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Amazon VPC Lattice - When Microservices No Longer Need to Take the Long Way Around to Communicate

Hello everyone, today I'll share a quick case study from AWS on how **Insurance Australia Group (IAG)** used **Amazon VPC Lattice** to improve service-to-service communication in a serverless architecture.

IAG is a major insurance company operating in Australia and New Zealand, running multiple systems serving customers such as insurance purchasing, contract management, and related services. They built their platform on AWS following a serverless approach with many microservices running on **AWS Lambda**. As the number of services increased, service-to-service calls began causing performance and complexity issues.

---

## The Problem Before

In the old architecture, when a Lambda called another service, the request didn't go directly but had to route through many layers like **Transit Gateway**, **Egress VPC**, proxy, the Internet, and finally to the **API Gateway** and the target service.

Even though the service remained within AWS, the request had to take quite a long route. This increased latency, made the architecture more complex and harder to manage. When one service called multiple other services, latency would compound. Additionally, endpoints were often hardcoded, making changes inflexible.

---

## What is Amazon VPC Lattice?

**Amazon VPC Lattice** simplifies connecting services within AWS. Instead of having to manage many networking layers yourself, services can communicate with each other through a shared layer that is more secure and easier to manage.

In this case, IAG used **Lattice Services** to expose Lambda functions, allowing services to communicate directly within the AWS network without needing to go through the Internet.

---

## The New Architecture

IAG deployed VPC Lattice by creating separate **Service Networks** for each environment such as **Development**, **Staging**, and **Production**. These networks are managed centrally and shared across corresponding accounts.

This approach clearly separates environments while remaining easy to manage. At the same time, they used **Auth Policy** to only allow traffic from valid VPCs, adding an extra layer of security control.

---

## The New Communication Flow

After using VPC Lattice, requests between services travel much shorter paths. Lambda only needs to call an internal domain, **Route 53** maps it to the Lattice service, and the request is forwarded directly to the target service's Lambda.

As a result, traffic no longer needs to go through the Internet or multiple intermediate layers, reducing latency and improving security.

---

## Results

After implementation, IAG recorded service-to-service latency reduced by **46% to 83%**, with **P95 latency** improved from **15% to 92%**. This is a significant improvement, especially for a system with many services calling each other.

Additionally, the architecture became simpler, easier to operate, and reduced dependence on complex networking components.

---

## Key Benefits

VPC Lattice helps significantly reduce latency, simplifies network architecture, and improves security because traffic doesn't need to go out to the Internet. Environment separation is also clearer, fitting well with multi-account systems. It's particularly well-suited for serverless architectures using Lambda.

The main benefits can be summarized as follows:

- Reduced latency when services communicate with each other.
- Simplified network architecture between microservices.
- Limited need for traffic to go through the Internet.
- Increased security control capability through Auth Policy.
- Suitable for systems with multiple environments and multiple AWS accounts.
- Good support for serverless architectures using AWS Lambda.

---

## A Few Notes

IAG is currently using **Auth Policy** at the Service Network level, meaning VPC-level control. In the future, they want more granular control at the individual service level.

Additionally, the event format from VPC Lattice differs from API Gateway, so teams need to update their handlers accordingly.

---

## Conclusion

Amazon VPC Lattice helps IAG solve a common problem in microservices: faster, simpler, and more secure communication. Instead of having to route through many network layers, services can call directly within AWS, significantly reducing latency and simplifying the architecture.

If you're building serverless or microservices systems on AWS, especially as the number of services grows, VPC Lattice is a very worthwhile option to consider.

---

## Reference Links

[https://aws.amazon.com/vi/blogs/networking-and-content-delivery/how-iag-accelerated-service-to-service-communication-with-amazon-vpc-lattice/](https://aws.amazon.com/vi/blogs/networking-and-content-delivery/how-iag-accelerated-service-to-service-communication-with-amazon-vpc-lattice/)

---

![Blog 1](/images/Blog1.png)


(/images/Blog1.png)