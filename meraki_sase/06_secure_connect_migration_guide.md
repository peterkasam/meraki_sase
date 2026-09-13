# Secure Connect to Secure Access Manual Migration Guide

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Solution_Overview/Secure_Connect_to_Secure_Access_Manual_Migration_Guide

## Overview

This guide helps organizations manually migrate from Cisco Secure Connect to Cisco Secure Access. The new platform is described as a "cloud-native Security Service Edge (SSE) platform" providing Zero Trust Access without traditional VPN friction.

**Key Point:** Manual migration requires downtime, and a Meraki organization cannot simultaneously connect to both platforms.

## Migration Requirements

Cisco validates migrations specifically for these solution designs:
- **Design A:** Cloud Spoke and Cloud Hubs
- **Design B:** Any Spokes with Hybrid and Cloud Hubs
- **Design C:** Hybrid Spokes with Local and Cloud Hubs

### General Considerations

- Larger policy volumes increase migration time
- More sites require longer downtime
- Supported: up to 2,500 AutoVPN tunnels and any IPSec tunnels
- **Recommendation:** Run 2025 Platform Optimization on Secure Connect before disconnecting sites

## Migration Limitations

- AutoVPN and IPSec connection migration requires downtime
- Support intervention needed to remove Secure Connect feature
- All related configurations must be removed before Support can assist (RAVPN regions, private applications, policies)
- Removing these configurations reduces revert capability

## Pre-Migration Preparation Steps

1. Identify all private applications with their IP addresses and ports
2. Determine the Identity Provider (IdP) for user authentication
3. Review existing Secure Connect policies, Cloud Firewall, and DNS rules
4. Consolidate unused or duplicate policies
5. Gain administrative access to Secure Access dashboard
6. Review the Getting Started Guide for readiness verification
7. Plan a maintenance window

## Migration Steps (Configuration Phase)

### Authentication

Configure SAML metadata exchange with your IdP. The same IdP can serve both platforms simultaneously without service interruption.

### Resources and Applications

Migrate private resources by:
- Creating private resource groups in Secure Access
- Defining resources with names, internally reachable addresses, protocols, and ports
- Configuring endpoint connection methods (VPN and/or Zero Trust)
- Adding resources to Connector Groups
- Repeating for each private resource to ensure matching details

### Posture Profiles

Two profile types require migration:
1. **Zero Trust network access profiles** (client-based and browser-based)
2. **VPN access profiles**

Copy all posture configuration elements exactly. Secure Access VPN profiles offer additional granular controls (optional for migration).

### Remote Access VPN End User Connectivity

1. **Configure IP Pools and Regions:** Navigate to Connect > Essentials > End User Connectivity; add regional IP pools with DNS servers
2. **Create VPN Profiles:** Define profiles with display names, domains, DNS, and IP pools; select protocols (IKEv2, SSL)
3. **Deploy to Cisco Secure Client:** Download new profile XML and deploy via device management

### Umbrella Core Identities and Policy Components

Verify Umbrella dashboard for configurations in Deployments and Policies tabs. If configurations exist, replicate them in Secure Access using the provided mapping table.

### Policy and Connectivity Validation

**Important:** Policies execute top-down. Maintain this execution order:
1. Zero Trust Access policies
2. Deny DNS policies
3. Deny Firewall policies
4. Deny Web policies
5. Allow policies
6. Default policies

**Note:** Migrate only deny policies; allow DNS and Firewall policies are redundant due to subsequent policy engines.

## Post-Configuration Steps

1. **Verification:** Test connectivity and policy enforcement
2. **Configuration Removal:** Remove all Secure Connect configurations before Support removes the feature
3. **Onboarding:** Contact Support to facilitate tunnel migration from Secure Connect to Secure Access

## API Resources Available

- Secure Connect private applications API
- Secure Access resource groups and connector APIs
- Umbrella deployments and policies APIs
- Secure Access deployments and access rule APIs

## Key Considerations

The guide emphasizes planning with full awareness of downtime requirements. Support assistance is limited to onboarding support (removing Secure Connect and route optimization). Organizations bear responsibility for configuration migration itself.
