# Cisco SASE: Onboarding/Offboarding Cisco SASE with Meraki SD-WAN

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Onboarding_Guide/Cisco_SASE%3A_Onboarding_Meraki_SASE

## Overview

This guide enables integration of Cisco Secure Access with Meraki SD-WAN through the Meraki dashboard. The process separates management experiences for NetOps and SecOps teams. Upon successful onboarding, a SASE tab appears in the Meraki dashboard for streamlined management.

## Prerequisites

Before starting, complete these requirements:

- Access to Security Cloud Control platform
- Set up your Secure Cloud Sign On account
- Claim your subscription in Security Cloud Control
- Create a Secure Access Integrations Meraki API Key with Admin privileges

**Important:** After onboarding completion, additional API keys generate automatically. "Do not remove any of the keys as it may cause system to get into stuck state."

### Create Secure Access Integrations Meraki API Key

1. Navigate to Cisco Secure Access dashboard
2. Click **Admin** in left navigation, then select **API Keys**
3. Click **Add** button (top right corner)
4. Enter API Key Name
5. Select **Integrations > MSA** under Key Scope
6. Set authority to **Read/Write**
7. Click **CREATE KEY**
8. Copy and securely save API Key and Key Secret (displayed only once)
9. Click **ACCEPT AND CLOSE**

## Integration Experience

1. In Meraki dashboard, navigate to **Organization > Integrations** (Configure section)
2. Select **Cisco Secure Access**
3. Click **Connect** (top right)
4. Verify SD-WAN environment doesn't require spoke-to-spoke communication over Hub
5. Copy and paste Secure Access Integrations Meraki API Key pair
6. Click **Add integration**

Upon completion, the dashboard displays integration details including user, date, and organization ID from Secure Access.

## Troubleshooting Integration Errors

| Error | Resolution |
|-------|-----------|
| Failed to create integration records | Retry request; contact support if persistent |
| Integration already queued | Retry request; contact support if persistent |
| Unable to retrieve organization ID | Verify API key type and scope (Admin > Integrations > MSA: Read/Write) |
| Already integrated with another Meraki organization | Verify correct Secure Access org; disconnect existing connection if needed |
| Organization not yet eligible | Contact support to enable feature flag |
| Organization not linked to Security Cloud Control | Contact support for SCC linking process |
| Internal error | Retry request; contact support if persistent |
| API key generation failure | Retry request; contact support if persistent |
| Integration failing to activate | Retry request; contact support if persistent |

## Disintegration (Disconnecting)

1. Navigate to **Organization > Integrations** (Configure section)
2. Click **My integrations**
3. Click **Secure Access Integration**
4. Click **Disconnect** (top right corner)

Disconnection completes within 10-15 minutes.

### Troubleshooting Disconnection Errors

| Error | Resolution |
|-------|-----------|
| Remove all Secure Access sites first | Detach all Sites before proceeding |
| Remove all Secure Access regions first | Removing last Site removes Region(s) automatically |
| Integration removal already queued | Wait briefly, then retry; contact support if persistent |
| Failing job due to removal failure | Wait briefly, then retry; contact support if persistent |
