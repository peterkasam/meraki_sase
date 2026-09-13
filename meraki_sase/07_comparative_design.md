# Meraki SASE: Comparative Deployment Design (A / B / C)

Companion design document to [05_design_best_practices.md](05_design_best_practices.md). Provides topology diagrams, control-plane/BGP behavior, a side-by-side comparison matrix, policy considerations, a migration runbook, and a decision framework covering all three documented deployment architectures.

## 1. Executive Summary

This design compares the three deployment architectures for integrating Meraki MX SD-WAN with Cisco Secure Access (SSE) into a unified SASE solution. Each design trades off east-west traffic inspection, hub hardware investment, routing complexity, and license package (SIA vs. SIA+SPA) differently.

All three designs share automatic onboarding behavior: dashboard-installed static routes are removed, BGP becomes the sole routing method, Meraki hubs stop re-advertising spoke routes to other spokes (disabling MX-hub spoke-to-spoke transit), and cloud spokes prefer the cloud path once enrolled.

### Recommendation at a Glance

| Criterion | Best Fit | Rationale |
|---|---|---|
| North-south heavy, 500+ sites, no hub hardware desired | **Design A** | Simplest control plane, smallest routing tables, cloud FW covers all east-west |
| Mixed traffic, need MX-fabric performance for some flows + cloud inspection elsewhere | **Design B** | Hybrid spokes get direct MX-fabric access to hubs; cloud spokes stay simple |
| SIA-only, existing MX hub investment, traditional SIG model | **Design C** | Preserves MX-fabric east-west performance; adds cloud gateway for internet only |
| RAVPN/ZTNA users must reach all site subnets | **A or B** | Design C blocks remote-access users from subnets behind local-hub-only spokes |

## 2. Dual-Fabric Terminology

**Cloud Fabric:** Cloud Spoke (enrolled exclusively in cloud transport) and Cloud Hub (Secure Access-enhanced head-end, platform type `CPSC-HUB`).

**MX (Local) Fabric:** Local Spoke (traditional Auto-VPN spoke) and Local Hub (MX in hub mode, in spoke hub-priority lists).

**Dual-Fabric Roles:** Hybrid Spoke (both cloud + MX fabric) and Hybrid/Enrolled Hub (MX hub meshing with other hubs while enrolled in the cloud fabric).

```
  Cloud Fabric                         MX (Local) Fabric
  ------------------------             ------------------------

   [ Secure Access ]                     [ Local Hub  ]
   [   Cloud Hub   ]<---iBGP--+           [   (MX)     ]
        ^     ^               |                 ^
        |     |               |                 |  Auto-VPN
     Auto-VPN |               |                 |  mesh
        |     |               +-----------------+
        |     |                        (Hybrid / Enrolled Hub)
   [Cloud   [Hybrid
    Spoke]   Spoke]---Auto-VPN---> [Local Hub / Hybrid Spoke]
```

## 3. Platform Optimization & Scale Limits

**Automatic onboarding changes:** static routes removed; BGP becomes sole routing method; MX-hub spoke-to-spoke transit disabled; cloud spokes prefer cloud path.

| Parameter | Limit |
|---|---|
| Enrolled sites per organization | Up to 1,000 |
| Routing prefixes advertised per site to cloud fabric | Max 10 (or default-route-only) |
| MX hybrid (enrolled) hubs — Early Access | Max 2 |
| MX hybrid (enrolled) hubs — small deployments | Up to 10 |
| Default-route-only eligibility | 500+ enrolled sites, via support request |

**Maintenance window** is required only when the org is SIA-only *and* needs spoke-to-spoke communication via an MX hub. SIA+SPA orgs need no maintenance window.

## 4. Design A — Cloud Spokes and Cloud Hubs

**Architecture:** Every MX network is a cloud spoke; no customer-owned local hub. Fits north-south-heavy, 500+ site, simplified deployments.

```
  Site 1 (Cloud Spoke) --Auto-VPN/iBGP--\
  Site 2 (Cloud Spoke) --Auto-VPN/iBGP---+--> [Secure Access
  Site 3 (Cloud Spoke) --Auto-VPN/iBGP---+      Cloud Hubs]
  Site N (Cloud Spoke) --Auto-VPN/iBGP--/        |
                                                  v
                                    Cloud Firewall / SIA / SPA
                                    (all E-W and N-S inspected)

  No MX hub deployed. No spoke-to-spoke MX transit possible.
```

**Control plane:** Each spoke runs iBGP to primary/secondary cloud hubs, receiving a default route (or specific prefixes). All east-west traffic transits the cloud fabric and is inspected — no bypass path.

**Advantages:** minimal config; supports SIA and SPA; smallest routing tables; no hub hardware; 100% E-W inspection; DIA breakout supported.

**Limitations:** no local/hybrid hub support; no path flexibility; no customer-owned backup path.

## 5. Design B — Any Spokes with Hybrid and Cloud Hubs

**Architecture:** Mixed cloud/hybrid/local spokes; hybrid (enrolled) hubs and cloud hubs both present — the flexible, balanced option.

```
        +------------------ Secure Access Cloud Hubs --------+
        |  iBGP                              iBGP            |
        |                                                    |
  [Cloud Spoke]                                    [Cloud Spoke]
        |                                                    |
        |            +---- Hybrid (Enrolled) Hub ----+       |
        |            |   (MX hub + cloud-enrolled)   |       |
        |            +---------------+---------------+       |
        |                    Auto-VPN mesh (MX fabric)        |
  [Hybrid Spoke]------------------+------------------[Local Spoke]
   (cloud + MX fabric)                              (MX fabric only)
```

**Control plane:** cloud/hybrid spokes run iBGP to cloud hubs; hybrid spokes additionally join the MX Auto-VPN mesh to reach hybrid/enrolled hubs directly. **Note:** hybrid-spoke-to-enrolled-hub traffic over the MX fabric bypasses the cloud firewall — compensate with local MX firewall policy.

**Advantages:** supports SIA/SPA; unified policy on cloud-fabric E-W traffic; low-latency MX-fabric path where needed; extra hub redundancy.

**Limitations:** extra MX hardware; dual-backbone routing complexity; MX-fabric E-W inspection gap.

## 6. Design C — Any Spokes with Local and Cloud Hubs

**Architecture:** Traditional SIG model — MX Auto-VPN fabric carries east-west traffic via local hubs; cloud fabric is purely an internet gateway (SIA-only fit).

```
   [Local Spoke]---Auto-VPN---+
   [Hybrid Spoke]-------------+---[ Local Hub / Hub Group ]
   [Local Spoke]---Auto-VPN---+        (MX fabric, E-W)

   [Hybrid Spoke]---iBGP------------> [Secure Access
   [Cloud Spoke]----iBGP------------->  Cloud Hub] --> Internet
                                        (N-S / SIA only)

   Cloud spokes CANNOT reach local-hub subnets or vice versa.
```

**Control plane:** local/hybrid spokes on the same hub group exchange routes via Auto-VPN through local hubs (not cloud-inspected); cloud spokes run iBGP to cloud hubs for internet egress only, with no reachability into the local-hub mesh.

**Advantages:** simple for SIA-only orgs; native MX performance for E-W; familiar SIG architecture.

**Limitations:** E-W traffic NOT inspected by cloud firewall; hybrid spokes must keep consistent hub-priority lists; RAVPN/ZTNA users cannot reach subnets behind local-hub-only spokes; future SPA adoption needs re-architecture.

## 7. Side-by-Side Comparison

| Dimension | Design A | Design B | Design C |
|---|---|---|---|
| Hub hardware required | None | Yes (hybrid/enrolled) | Yes (local) |
| Licensing | SIA or SPA | SIA or SPA | SIA (SIG-style) |
| East-west inspection | 100% via cloud FW | Partial (MX-fabric E-W not inspected) | None (all E-W via local MX fabric) |
| Routing complexity | Low | High (dual backbone) | Medium (isolated fabrics) |
| Best site-count fit | 500+ | Any | Any, esp. existing MX hub estates |
| Backup/failover path | Cloud only | Cloud + local hub redundancy | Local hub redundancy; cloud for internet only |
| RAVPN/ZTNA reachability | Full | Full | Partial (blocked behind local-hub-only spokes) |
| Maintenance window on enrollment | Not required (typ.) | Required if SIA-only + spoke-spoke via MX hub | Required if SIA-only + spoke-spoke via MX hub |
| Path to future SPA adoption | Native | Native | Requires re-architecture |

## 8. Policy Design Considerations (All Designs)

| Console | Responsibility |
|---|---|
| Meraki Dashboard | Local firewall policies on MX; group policies; content filtering/threat protection |
| Secure Access Dashboard | Cloud security profiles/policy; Network Tunnel Group source/destination objects |

**Known limitation:** "Internal Networks" objects cannot be associated with Meraki Network Tunnel Groups in Secure Access — use the Network Tunnel Group / site object directly for granular policy sourced from Meraki sites.

## 9. Migration & Onboarding Runbook (Design-Agnostic)

1. Confirm licensing (SIA vs. SIA+SPA) — gates whether a maintenance window is mandatory.
2. Enable the Secure Access integration at the org level.
3. Onboard hub(s) first (Designs B/C) — confirm Hub Mesh enabled, no MX Exit Hub configured.
4. Onboard spokes via SASE > Connect > Sites; use templates for bulk enrollment.
5. Re-enable eBGP on any spoke that used it previously; enable "allow transit" on hub eBGP peers.
6. Validate via Security & SD-WAN > Monitor > VPN Status; check prefix counts against the 10-prefix limit.
7. SIA-only orgs needing spoke-to-spoke over MX hubs: engage support post-enrollment for the maintenance-window activity.
8. At 500+ sites, consider requesting default-route-only advertisement.
9. Build/validate Secure Access policy objects before cutover, given the Internal Networks limitation.

## 10. Decision Framework

1. Existing/planned customer-owned MX hub investment to preserve? **No → Design A.**
2. Need 100% cloud-FW inspection of east-west traffic with zero MX-fabric bypass? **Yes → Design A** (or B with compensating local policy). If not required and SIA-only → **Design C** acceptable.
3. Do RAVPN/ZTNA users need reachability to every site, including any behind a local-only hub? **Yes → eliminate Design C** for those sites (or make them hybrid spokes).
4. Is SPA on the roadmap even if unpurchased today? **Yes → prefer A or B**; C requires rework.
5. Latency-sensitive/high-bandwidth site-to-site flows justifying a direct MX-fabric path? **Yes → Design B** (or C if SIA-only and E-W inspection isn't required).

## 11. References

- [05_design_best_practices.md](05_design_best_practices.md) — Cisco Secure Access Integration – Design Best Practices
- [03_sites_connectivity.md](03_sites_connectivity.md) — Cisco SASE: Sites Connectivity
- [04_policy.md](04_policy.md) — Cisco SASE: Policy
- [06_secure_connect_migration_guide.md](06_secure_connect_migration_guide.md) — Secure Connect to Secure Access Migration Guide
