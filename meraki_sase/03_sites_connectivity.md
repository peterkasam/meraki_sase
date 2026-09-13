# Cisco SASE: Sites Connectivity

Source: https://documentation.meraki.com/SASE_and_SD-WAN/MX/Integrations/SASE/Getting_Started/Cisco_SASE%3A_Sites_Connectivity

## Overview

The integration enables Meraki SD-WAN branches to connect with Secure Access cloud fabric through the SASE > Connect > Sites menu. Key details include:

- Uses Meraki Auto-VPN tunnels for both spoke and hub appliances
- "Onboarding a Meraki organization to Secure Access applies platform optimization features automatically"
- MX Spokes establish tunnels with cloud hubs via iBGP, installing default routes
- MX Hubs install all known prefixes except default routes

## Prerequisites

- Hub mesh must be enabled for proper MX hub integration
- "Using an MX Hub as an Exit Hub is not supported when using Secure Access integration"
- Spokes with eBGP require routing protocol re-enablement after enrollment
- Organizations with only hubs connected to Secure Access will route hub-to-hub traffic over Auto VPN mesh, not through Secure Access
- All prefixes from Meraki AutoVPN and IPSec sites must be unique IP subnets

## SASE Sites Management

### Enrolling a Site

Steps include:
1. Navigate to SASE > Connect > Sites
2. Click **Add Site** button
3. Select Meraki branches and assign to Secure Access region
4. Review selections and click **Next**
5. Confirm with **Save and Finish**
6. Verify enrollment at Security & SD-WAN > Monitor > VPN Status

**Note:** "When enrolling a large number of sites, using templates allows the administrator to do so in one step."

### Detaching a Site

1. Navigate to SASE > Connect > Sites
2. Select the Meraki branch to unenroll
3. Click **Detach site** button
4. Confirm detachment

## MX Spoke Configuration

When enrolled in Secure Access, MX Spokes:
- Establish tunnels and iBGP peering with cloud hubs
- Install default routes pointing to primary and secondary hubs
- Advertise subnets enabled for VPN mode
- Can have default routes disabled via "Manage default route" option in Early Access feature

## Secure Access Spoke Default Route Management

An Early Access feature allows administrators to:
- Disable default routes on individual spokes
- Configure spokes to receive only specific routes instead
- Preserve East/West connectivity while leveraging MX WAN default route for Internet access

## MX Hub Integration

### Key Requirements

1. **Hub Mesh:** Must be enabled; contact Meraki support if disabled
2. **Exit Hubs:** Not supported with Secure Access
3. **Multiple Default Routes:** IPv4 default route should not be enabled on hubs that spokes use for direct application access
4. **eBGP Configuration:** "The 'allow transit' option should be enabled on the eBGP peer configuration"
5. **Hub-Only Organizations:** Hub-to-hub traffic routes over Auto VPN mesh; apply firewall policies for security

### Default Route on Hubs

Hubs can enable default routes via SASE > Connect > Sites drawer, allowing them to receive default routes plus specific prefixes from Cisco SSE.

## Integration Features

- Meraki AutoVPN sites can be used as source criteria in Secure Access Policy
- Individual sites selectable under Network Tunnel Groups in both Private and Internet rules
- Traffic statistics and Meraki Dashboard links available in Secure Access UI

## API Endpoints

**Managing Integration:**
- Get/Create/Get/Delete organization SASE integration

**Managing Sites:**
- Get SASE regions
- Create/Delete site enrollment
- Get/Update individual sites
- Get connectivity overview
