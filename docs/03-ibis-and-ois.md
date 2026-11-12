---
search:
  exclude: true
---

# IBIS and OIS Stabilization

[← Lens communication](02-lens-body-communication.md) · [Overview](index.md) ·
[Next: Aperture ring →](04-aperture-ring.md)

This chapter maps parts of OM stabilization control and compares them with
available Lumix material. Forty-nine bounded OM-5 CPU diagnostics clarify
software classification and mask selection; they do not demonstrate physical
roll correction or Panasonic OIS working in a hybrid mode on OM-5.

## Four stabilization regimes

Lens OIS typically covers pitch and yaw. Five-axis IBIS adds roll and X/Y
translation — correction dimensions, not necessarily five independent actuators.
The regimes that must stay distinct:

| Regime | What happens |
|---|---|
| Lens OIS only | Body stabilization is not used |
| Coordinated (OM Sync IS / Panasonic Dual I.S.) | Lens and body share correction through a supported protocol |
| Orthogonal split | Separate dimensions are assigned to otherwise independent systems |

Enabling two mechanisms at once is not evidence of coordination; overlapping
loops can interact, and effectiveness has to be measured.

## OM stabilization records

The survey (E-M5/E-M1 through OM-3) found gyro-related records, channels named
Y/X1/X2, synchronization state, and body/lens allocation data. The inspected
records carry yaw/pitch split fields and roll-related body data but no
`RATIO_ROLL` field — which does not prove roll is active in every state. The
`luke_main` component holds CPU0/CPU1 and drive-task material; it is a controller
lead, not a reconstructed servo. The physical relationship between Y/X1/X2 and
sensor rotation is not established by the current tests.

## What the 49 CPU diagnostics established

| Element | Bounded result |
|---|---|
| Protocol/version helper | Not a manufacturer predicate |
| Maker helpers | Separate the tested OM and Panasonic/Leica identities |
| OIS classifier `0x602176a1` | Class 1 for tested OM OIS fixtures, class 2 for non-OM OIS fixtures |
| Caller around `0x6058e6f7` | Considers the extra branch only for class 1, under its other conditions |
| Restricted helper `0x601224f3` | Returns value `4` in the tested restricted cases |
| Exposure-field transport | Carries the examined halfword and byte separately into downstream logic |

Tests used synthetic descriptor and RAM states and stop at explicit call/return
boundaries. An earlier claim that stock OM-5 already gives Panasonic OIS
body-side roll-only correction was **withdrawn**: the evidence shows a
classification difference, not a demonstrated hybrid capability.

## Two unresolved boundaries

| Boundary | What is known | What is missing |
|---|---|---|
| Exposure selection | `0x6206cf74` comes from `original_request + 4`; the byte `0x6206cf86` from `original_request + 31`; the exposure branch selects on the former | Its final producer and real values during exposure; it must not be relabeled an IS type or menu value |
| Controller interpretation | Header, vector, and startup fix CPU0's mapping in `010e` | CPU1's mapping, the mask receiver, bit meaning, and actuator effect; mask `4` as a roll restriction stays a hypothesis |

## Lumix generations and available material

| Family | Panasonic tier | Material available here |
|---|---|---|
| GM1 / GM5 | No IBIS | GM5 decoded locally; useful lens-OIS reference |
| GX8 | Dual I.S. | Container research; no verified IBIS plaintext used |
| GX80 / GX85, GX9 | Dual I.S. (not Dual I.S. 2) | Vendor compatibility material and external research |
| G80 / G81 / G85, GH5, G9 | Dual I.S. 2 | Vendor matrix only; no verified coordination code used |
| Later bodies | Model/firmware dependent | Not evidence of one algorithm or protocol across generations |

G80/G85 and GX80/GX85 are different families, and five-axis IBIS does not imply
Dual I.S. 2. Vendor tables list combinations and firmware requirements, not a
packet specification. Update-file encryption is also a separate question from any
encryption of traffic across the mount; this workspace has no usable plaintext
for a Lumix IBIS body and makes no claim about worldwide key availability.

## Investigate next

Look for correction-mask producers and consumers, exposure-message semantics,
controller relocation, lens-priority arbitration, and the full path from software
classification to actuator commands. For Lumix, seek material tied to a specific
IBIS body and lens rather than extrapolating from GM5. The proposed
[hybrid direction](07-future-research.md#hybrid-lens-ois-and-body-correction)
depends on these missing links; no IS modification is supplied.
