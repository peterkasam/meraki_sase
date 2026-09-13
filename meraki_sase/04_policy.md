# Cisco SASE: Policy

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Getting_Started/Cisco_SASE%3A_Policy

## Overview

This documentation page covers policy configuration for Cisco SASE integrated with Meraki SD-WAN. Security policies are fundamental to organizational cybersecurity frameworks, controlling access to network resources, applications, and data while ensuring only authorized users and devices can connect.

## Key Principles

Security policies enforce three core measures: authentication, authorization, and accountability. These typically include "multi-factor authentication, role-based access control, network segmentation, and continuous" monitoring to strengthen defenses and reduce breach risks.

## Policy Configuration Guidelines for Meraki Platform

Two separate dashboards manage policies in this integrated environment:

- **Meraki Dashboard**: Configure firewall policies enforced locally on MX devices
- **Secure Access Dashboard**: Configure cloud security profiles and policies

For Meraki-side configuration, refer to:
- Creating and Applying Group Policies documentation
- MX Firewall Settings guide
- Content Filtering and Threat Protection resources

## Policy Configuration Guidelines for Secure Access

Administrators should follow "Secure Access Policy" configuration guides to establish necessary security policies in the cloud component.

### Known Limitation

Currently, "Internal Networks" cannot be associated with Meraki Network Tunnel Groups within Secure Access.

## Related Resources

- Cisco SASE: Getting Started Guide
- Cisco SASE: Sites Connectivity documentation
