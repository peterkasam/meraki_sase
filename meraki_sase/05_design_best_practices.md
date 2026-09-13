# Cisco Secure Access Integration - Design Best Practices

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Solution_Overview/Cisco_Secure_Access_Integration_-_Design_Best_Practices

## Overview

This documentation outlines deployment options for integrating Meraki MX SD-WAN with Cisco Secure Access to deliver a comprehensive SASE solution. The guide emphasizes that "following these recommended designs will help ensure your network achieves optimal performance and security."

### Dual Fabric Terminology

The document defines key terms for understanding two SD-WAN transport mechanisms:

**Cloud Fabric Components:**
- Cloud Spoke: Meraki device enrolled exclusively in cloud transport
- Cloud Hub: Secure Access-enhanced head-ends (CPSC-HUB platform type)

**MX (Local) Fabric Components:**
- Local Spoke: MX in spoke mode using traditional Meraki tunnels
- Local Hub: MX in hub mode appearing in spoke hub priority lists

**Dual-Fabric Devices:**
- Hybrid Spoke: Uses both cloud and MX fabric for traffic
- Hybrid (Enrolled) Hub: MX hub forming mesh with other hubs while enrolled in cloud fabric

---

## Cisco SASE Supported Topologies

When deploying SASE architecture, "it is recommended to inspect East-West traffic between sites to maximize the security efficacy of the security services." The integration disables direct spoke-spoke communication via MX Hub to ensure all traffic receives inspection and policy application.

### Platform Optimization

Key changes adopted at onboarding:

- Dashboard-installed routes removed from all spokes and hubs
- BGP becomes sole routing method
- Meraki hubs prevented from sharing spoke routes to other spokes
- Cloud spokes prefer cloud path for all site communication

**Scale Limits:**
- Up to 1,000 enrolled sites
- Maximum 10 routing prefixes per site advertised to cloud fabric
- Option to receive only default route for faster convergence
- Early-access deployment: maximum 2 MX hybrid hubs; small deployments support up to 10

---

## Maintenance Window Requirements

Organizations need a maintenance window if they have:
1. Secure Internet Access (SIA) package only (without SPA)
2. Require spoke-spoke communication using MX hubs

**No maintenance window needed** for organizations with SIA + Secure Private Access (SPA) packages.

### Enabling Integration Steps

1. Enable integration via dashboard
2. Onboard hubs and spokes to SSE
3. For SIA-only customers requiring spoke-spoke communication, contact support after enrollment
4. Organizations with 500+ enrolled sites can request default-route-only configuration

---

## Design A: Cloud Spokes and Cloud Hubs

**Architecture:** All MX networks operate as cloud spokes with no local hub deployment.

### Traffic Profile Fit

Best for organizations with "traffic primarily north-south toward the internet." Suitable for 500+ site deployments seeking simplified architecture without hub hardware maintenance.

### Advantages
- Minimal configuration required
- Supports both SIA and SPA use cases
- Small routing tables at spokes
- No hub hardware deployment needed
- Cloud firewall protection for east-west traffic
- Direct internet (DIA) breakout supported

### Limitations
- No support for local or hybrid hubs
- Limited traffic path flexibility
- No customer-owned backup for cloud transport

---

## Design B: Any Spokes with Hybrid and Cloud Hubs

**Architecture:** Mix of cloud spokes, hybrid spokes, and local spokes with flexible connectivity options.

### Traffic Profile Fit

Supports varied traffic patterns by leveraging both Meraki SD-WAN and cloud fabrics. Enables selective east-west traffic routing through Meraki fabric when needed while maintaining cloud inspection for other flows.

### Advantages
- Supports both SIA and SPA use cases
- Spoke-to-spoke traffic traverses cloud with unified security policy
- Hybrid spokes access hubs directly via MX fabric for performance optimization
- Cloud spokes maintain simple configuration
- Faster convergence during onboarding and redundancy
- Additional redundancy from local and enrolled hubs

### Limitations
- Requires additional MX hardware for hubs
- Dual-backbone routing increases complexity
- Hybrid spoke-to-enrolled hub traffic bypasses cloud firewall

---

## Design C: Any Spokes with Local and Cloud Hubs

**Architecture:** Centered on traditional SIG model where MX SD-WAN fabric handles east-west traffic with cloud gateway for internet access.

### Traffic Profile Fit

Aligns with "organizations that purchase only the Secure Internet Access (SIA) package." Maintains traditional infrastructure while adding cloud-based internet protection.

### Connectivity Configuration

**Local Hub Reachability:** Local spokes communicate with configured hub applications but cannot reach cloud spokes or SSE-enrolled endpoints.

**Spoke-to-Spoke Communication:** Hybrid and local spokes sharing the same MX hub group communicate directly; cloud spokes cannot participate in this local fabric communication.

**Remote Access Limitations:**
- RAVPN/ZTNA users cannot access subnets behind local hubs
- Networks behind hybrid spokes remain fully reachable by remote workers

### Advantages
- Simplified deployment for SIA-focused organizations
- Spoke-to-hub traffic maintains MX platform performance
- Familiar SIG-style architecture prioritizing MX transport

### Limitations
- East-west traffic not inspected by cloud firewall
- Hybrid spokes must maintain consistent hub priority lists
- Future SPA adoption requires reconfiguration

---

## Key Takeaways

Organizations should select designs based on traffic patterns and security requirements. Design A suits large-scale retail deployments, Design B provides balanced flexibility, and Design C maintains traditional infrastructure models. All designs support organization scaling objectives documented in the platform optimization guidelines.
