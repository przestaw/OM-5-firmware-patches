---
search:
  exclude: true
---

# Lens-Body Communication

[← Architecture](01-architecture-and-evidence.md) · [Overview](index.md) ·
[Next: Stabilization →](03-ibis-and-ois.md)

This chapter reconstructs selected OM-5 lens-status paths and separates shared
MFT behavior from vendor extensions. It is not a complete MFT specification and
not a bus trace from a physical camera and lens.

## Interfaces are not interchangeable

Cross-brand MFT interoperability does not imply support for every optional
feature. Electronic aperture rings, OM Sync IS, and Panasonic Dual I.S. each
need extra capabilities, messages, and camera-side decisions. The lens-mount
interface must also be kept separate from internal body services.

| Name | Role in the analysis |
|---|---|
| `bc_sio` | Lens-facing serial transport and its diagnostics |
| `mcon` | Internal management/configuration interface, not a wire format |
| `per sio i2c` | Peripheral I2C diagnostics, not mount traffic |
| `luke_main` | Internal controller component, relevant to stabilization |

Finding a service name does not reveal electrical timing or the exact bytes sent
across the mount.

## Status requests

| Request form | Observed software role |
|---|---|
| `C1 80` | Lens-status path used by the aperture-ring experiment |
| `C1 00` | Another status form, deliberately left unextended |
| `81 E3` | Status path with an extra tail handled by stock code |

Parser `0x6021f6ca` and the dispatcher paths read variable records, options,
LAF/APACT fields, and extra status data; response length and cursor position
cannot be inferred from the request opcode alone. The ring experiment adds only
a two-byte value on selected forms and never combines it with the IS-extra
option — a chosen compatibility boundary, not evidence that arbitrary extensions
are safe.

## Identity is not protocol version

Native CPU tests separated two checks that earlier notes had confused.

| Item | Established meaning |
|---|---|
| `0x6021c999` | Protocol/version predicate on `L_FT_VERSION` (excludes sentinel `0x0b0a`, tests mask `0x0a`) — **not** a manufacturer test |
| `descriptor + 0x444..0x449` | Six-byte `L_ID`, used for identity/maker comparisons (`00 00` vs `02 00`/`03 00`) |
| `descriptor + 0x440`, bit `0x80` | Capability flag used by the ring experiment |

The six-byte identity is not shown to be a unique per-unit serial, so the ring
state also depends on session invalidation, not only on comparing identity bytes.

## Proprietary OM operations

A cross-model survey (E-M5/E-M1 through OM-3) found an evolving family of private
lens operations — among them `SetActLV_Private`, `SetISLDsyncAP_Private`,
`ContExpEnd_Private`, `ContIS_Private`, and `SetIsAsync_Private` — with later
images adding asynchronous and high-speed synchronization names.

| This survey shows | It does not show |
|---|---|
| Which private names exist per generation | Identical runtime behavior across bodies |
| That OM defines these operations | That a Panasonic lens accepts an OM command |
| A lead for where to look next | That a missing debug string means a missing function |

## The Lumix GM5 reference

GM5 has no IBIS, so it is a clean reference for a Lumix body that relies on lens
OIS alone. Its factory diagnostics expose OIS, gyro, Hall/reference, zoom, focus,
and communication labels — leads for lens telemetry and standalone OIS. They are
not a capture of live traffic during photography, do not fix which values the
body reads continuously, and are not a decoded implementation of Dual I.S. No
cross-brand stabilization protocol follows from diagnostic strings alone.

## Investigate next

Look for lens-status parsers, request-option handling, identity producers,
capability discovery, and the gap between internal command IDs and actual
transmitted frames. Electrical framing, units, timing, and recovery from missing
replies remain open. Focus-step tables and the focus-breathing admission issue
appear with the [stacking candidate](05-focus-stacking.md), not as a general
claim that all MFT lens behavior is decoded.
