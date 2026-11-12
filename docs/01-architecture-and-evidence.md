---
search:
  exclude: true
---

# Architecture and Evidence

[Overview](index.md) · [Next: Lens-body communication →](02-lens-body-communication.md)

All addresses in these notes are OM-5 Mark I firmware 1.2 unless stated
otherwise. A correctly decoded image is an input to analysis, not proof that a
modification boots or behaves correctly on a camera.

## How to read the evidence

Every claim in these chapters carries an implicit tier. The chapters mark the
weaker ones explicitly.

| Tier | Meaning |
|---|---|
| Static finding | Bytes, instructions, records, or references seen in a specific image |
| Bounded CPU result | Instructions executed with declared memory, synthetic inputs, and explicit stop points |
| External report | A vendor statement or another researcher's result — credited, not re-observed |
| Hypothesis | An interpretation with a stated test still outstanding |
| Unknown | A boundary the available material cannot resolve |

A name in a debug string is a lead, not a guarantee that a command is reachable,
supported on every body, or matched by a real physical response.

## Target identity

| Input | Value |
|---|---|
| Camera | OM SYSTEM OM-5 Mark I, E144/IM030 |
| Firmware | 1.2, build 1200 |
| Update container | 92,274,688 bytes; SHA-256 `a0c91f3a…c5943d11` |
| Decoded main component | 24,903,616 bytes; SHA-256 `cf4babea…2980af88` |
| Main analysis base | `0x60021020` |

Hashes identify inputs; they neither supply the files nor grant any right to
redistribute them. Addresses and structure sizes do not carry over unchanged to
E-P7, OM-3, OM-5 Mark II, or any other build.

## Component and section map

The update holds a main application component, UI/resources, a controller
component (`luke_main`), and one more component. Main is an MN10300/AM33 target
with uITRON structures. Its ten-section descriptor table sits at decoded offset
`0x6fdc` (CPU `0x60027ffc`); for uncompressed sections, subtracting the analysis
base turns a CPU address into a decoded file offset.

| Region | Note |
|---|---|
| Sections 6 and 7 | `olycompress` LZ4 streams — on-disk bytes are not expanded instructions |
| Section 1 load ceiling `0x6023e800` | Experiments stay below it without changing the loader |
| File area from `0x60257800` | Holds initialized data, not free space |

Recognizable-looking disassembly of compressed data is not evidence, and padding
in an update file is not an allocation contract: a region may still have
initialization, overlay, DMA, interrupt, or concurrency interactions that static
inspection cannot see.

## What the CPU experiments do and do not show

| Established within declared bounds | Not established |
|---|---|
| Selected stock and modified instruction paths run as described | Reset boot, recovery, and task scheduling |
| Result values, buffer and stack guards, register preservation | Sensor output, actuator motion, physical AF |
| Error propagation and fallback behavior | Stabilization efficacy or image quality |
| Specific landmarks and their roles | A combined image: separate module tests do not qualify one |

Some ring tests use declared property-lookup and timer fixtures; COLOR tests use
synthetic buffers and stop before the capture/ISP pipeline; the IS diagnostics
cover selected helpers and blocks, not the full exposure sequence.

## Method, for anyone continuing

The approach is reproducible in principle without any file from this workspace:
decode the official container, map the sections, then execute selected stock and
candidate paths in a strict CPU emulator that faults on unknown instructions and
unmapped memory, comparing stock against modified behavior and stopping at
explicit boundaries. What a continuation needs is a verified toolchain, declared
fixtures, provenance for every input, and evidence tied to the exact candidate
examined. These notes point to those concepts; they do not provide or run such
an environment.
