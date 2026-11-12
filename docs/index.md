---
search:
  exclude: true
---

# OM-5 Firmware Research

Research snapshot: **12 November 2026**. Target: **OM SYSTEM OM-5 Mark I,
E144/IM030, firmware 1.2**.

These notes cover lens-body communication, stabilization, the electronic
aperture ring, focus stacking, and the native COLOR engine. They separate
established software behavior from experimental proposals and from physical
behavior that was never tested. The scope is selected firmware paths — not a
full reconstruction of the MFT standard, an implementation of Panasonic
Dual I.S., or a camera-ready release. No modification here was verified on
hardware.

## Status at a glance

| Topic | Strongest result | Hard limit |
|---|---|---|
| Lens-body communication | Status formats, parser behavior, identity and protocol predicates mapped | No wire specification; no electrical bus capture |
| Stabilization | Cross-generation survey; 49 bounded CPU diagnostics | Physical axes, exposure behavior, and the controller receiver unresolved |
| Aperture ring | Complete software route; 6,837 bounded CPU runs, no unexpected failures | Runtime ownership, physical lens behavior, boot, recovery unverified |
| Focus stacking | 8-byte eligibility candidate; 69 CPU runs | Qualification blocked; admission policy changes |
| COLOR engine | Single-recipe prototype; 93 cases / 157 CPU runs | No capture/ISP validation, editable UI, or film library |
| Sensor rotation / hybrid IS | Scoped concepts with prerequisites | No implementation; no measured benefit |

These are historical results from the research workspace. This Markdown
publication does not ship the code, traces, or machine-readable evidence needed
to rerun them.

## Chapters

| # | Chapter | Covers |
|---|---|---|
| 1 | [Architecture and evidence](01-architecture-and-evidence.md) | Firmware identity, component and section mapping, and the evidence tiers used throughout |
| 2 | [Lens-body communication](02-lens-body-communication.md) | Mount traffic vs internal messages, status requests, and identity vs protocol predicates |
| 3 | [IBIS and OIS stabilization](03-ibis-and-ois.md) | Stabilization architectures, classifier diagnostics, and the Lumix Dual I.S. families |
| 4 | [Electronic aperture ring](04-aperture-ring.md) | The experimental software route, native getters, aperture limits, and its limits |
| 5 | [Focus stacking](05-focus-stacking.md) | Bracketing vs stacking eligibility, focus-breathing data, and the blocking instruction |
| 6 | [Caduceus-style COLOR](06-caduceus-color.md) | The native 12-sector engine, porting pitfalls, and the bounded prototype |
| 7 | [Future research](07-future-research.md) | Sensor rotation and hybrid OIS proposals, and the evidence each still needs |

Reference: [Sources and references](sources-and-references.md) ·
[Research notice (EU)](disclaimer.md).

## Scope

This repository holds research summaries and directions for further reading.
It omits tools, firmware, replacement bytes, workspace paths, and any
reproduction or installation steps. Nothing here authorizes redistribution of
third-party material.
