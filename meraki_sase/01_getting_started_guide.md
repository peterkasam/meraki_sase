# Cisco SASE: Getting Started Guide

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Getting_Started/Cisco_SASE%3A_Getting_Started_Guide

## Overview

The Cisco SASE onboarding process involves updating MX firmware to version 19.1 or later, claiming subscriptions in Security Cloud Control (SCC), and creating API keys for Secure Access integration. This enables "multi-console management of SD-WAN in Meraki and security policy management in Secure Access dashboards."

**Key Features:**
- SD-WAN management handled via Meraki dashboard for network operations
- Security policies managed through Secure Access dashboard for security operations
- Teams work in specialized domains while maintaining network security

## Prerequisites

### MX Firmware Update
- MX firmware version 19.1 or later required for standard Meraki MX hardware
- New models (8111-G2-MX, 8455-G2-MX) released after January 2026 require firmware 26.1.4
- Verify hardware compatibility and upgrade via Organization > Configure > Firmware Upgrades

### Maintenance Window Consideration
Schedule maintenance before integrating if you have:
- Only the Secure Internet Access package (no Secure Private Access)
- Meraki Hubs requiring spoke-to-spoke communication through an MX hub

## Onboarding Requirements

### Secure Access Considerations
- Organizations created before July 2, 2025 may need a flag enabled
- Must be managed with Security Cloud Control
- Multi-Org management supported (1:1 integration ratio only; no 1:Many or Many:1 combinations)

### Meraki Considerations
- New MX models post-January 2026 supported with firmware 26.1.5
- Umbrella DNS and SASE integration are incompatible
- Secure Connect and Secure Access cannot coexist on the same Meraki organization

## Platform Optimization

Recommended settings apply automatically at integration. For organizations managing over 500 sites in a single region, the "only-default-route for Spokes" feature reduces routing overhead by having spokes learn only default routes from hubs.
