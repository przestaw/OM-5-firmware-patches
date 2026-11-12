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
| Update container | 92,274,688 bytes; SHA-256 `a0c91f3ac7caaf0925b2af609e711183cf3b5982b3333224bcef26c0c5943d11` |
| Decoded main component (`0x0100`) | 24,903,616 bytes; SHA-256 `cf4babeab5f516514e4a9b6032b0374e643830511e083b9cdd26bcb02980af88` |
| Decoded IS/drive MCU (`0x010e`, `luke_main`) | 262,080 bytes; SHA-256 `b61feee4954ac5ea46d9b07677e68e1190a7eba69cffe1a8e46d058eeb94ab3b` |
| Main analysis base | `0x60021020` |

Hashes identify inputs; they neither supply the files nor grant any right to
redistribute them. Addresses and structure sizes do not carry over unchanged to
E-P7, OM-3, OM-5 Mark II, or any other build.

## Component and section map

The update holds main (`0x0100`), UI/resources (`0x0101`, `0x0106`), a controller
component (`0x010e`, `luke_main`), and component `0x0102`. Main is an
MN10300/AM33 target with uITRON structures. Its ten-section table sits at decoded offset
`0x6fdc` (CPU `0x60027ffc`); for uncompressed sections, subtracting the analysis
base turns a CPU address into a decoded file offset.

| Region | Note |
|---|---|
| Sections 6 and 7 | `olycompress` LZ4 streams — on-disk bytes are not expanded instructions |
| Section 1 load ceiling `0x6023e800` | The ring payload ends below it without changing the loader |
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

The landmarks and example expectations support an independent investigation:
identify the matching official container, map its sections, then compare selected
stock and candidate paths in a strict CPU emulator with explicit memory regions
and stop points. A candidate must be independently implemented or separately
obtained; it cannot be reconstructed exactly from these notes alone.

| Research goal | What these notes supply | What the next researcher still needs |
|---|---|---|
| Check a static finding | Input hashes, addresses, fields, and section boundaries | Lawfully obtained inputs, a decoder/disassembler, and a verified address map |
| Check a representative CPU result | Example inputs, expected outputs, and scope limits in the relevant chapter | A harness with the required registers, stack, memory and declared service fixtures |
| Repeat the exact historical totals or binary | Reported totals and upstream reference revisions | The original candidate sources, complete fixture matrix, engine build and retained evidence; these are not distributed here |
| Establish camera behavior | Explicitly identified gaps | Separate hardware validation; no result here substitutes for it |

The recorded GNU engine SHA-256 is
`e3a3b4a2dd7b7827a64221eac8cada53c9f341a66ce66399cdf2a156db62f703`.
Its source revision was `87ffb882e0f4d73401a0746177ffabb8e8bbe4ac`.
The original build helper rejected the downloaded archive's checksum; the
research used a separately recorded source-verification route. These identifiers
are provenance, not a promise that an ordinary upstream build reproduces the
same binary. An independent run should record its own toolchain and engine
identity rather than bypass a failed checksum check.
