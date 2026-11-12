---
search:
  exclude: true
---

# Caduceus-Style COLOR Research

[← Focus stacking](05-focus-stacking.md) · [Overview](index.md) ·
[Next: Future research →](07-future-research.md)

OM-5 firmware contains a native 12-sector COLOR builder and related processing
structures. A fixed synthetic recipe hosted on Color Creator passed **93 cases
and 157 bounded CPU runs**. This is not a complete port, a calibrated film
simulation, or a hardware-verified color feature. Caduceus E-P7 and Caduceus
OM-3 are foundational prior work, not projects responsible for or endorsing this
independent OM-5 experiment.

## Native processing structures

| Landmark | Role in the investigation |
|---|---|
| `0x6055a934` | Native base COLOR table |
| `0x603e2f1c` | Twelve-sector builder |
| `0x603e3106` | Selector |
| `0x603e31ad` | Prototype selector integration point |
| `0x603e4200` | Prototype legacy hue-stage integration point |

Finding the call graph and writer routines does not mean the whole capture job
or the hardware ISP path was executed. A source camera's menu controls and a
target camera's controls need not expose the same internal options.

## Porting pitfalls

| Detail | Why it matters |
|---|---|
| Capture-job allocation is 15,484 bytes (E-P7: 15,488) | A four-byte difference invalidates blindly copied offsets |
| Sector mapping is `w(r) = 2*(r mod 6) + floor(r/6)` | Non-linear; wheel index 1 maps to record 6 in the tested example |

These are porting constraints, not evidence that all recipe formats or picture
operations are interchangeable.

## The bounded prototype

The experiment intercepts two Color Creator selector keys, calls the native
builder, applies one fixed synthetic sector adjustment, marks the state dirty,
and suppresses the original hue-ring stage for the active host. Non-host modes,
explicit bypass, and external-profile cases were compared against stock output
within the tested bounds. The footprint is a 165-byte payload plus 16 bytes of
hooks, all in an already-loaded uncompressed section. The adjustment does not
reproduce a named film, and existing Color Creator controls do not yet edit the
recipe.

| The 93 cases include | They stop short of |
|---|---|
| Neutral and single-sector output | The whole capture lifecycle (they end before `0x603e1192`) |
| Host/non-host behavior, bypass, external profiles | Remaining LUT submission and real ISP pixels |
| Synthetic cold/warm cache states and dirty bits | A hardware cache-coherence proof |
| One integrated pipeline slice | Runtime ownership, DMA, scheduling, boot/recovery, LV/JPEG/ORF output |

A key distinction: here, **neutral native COLOR is not unchanged stock Color
Creator** — the prototype replaces ordinary host behavior even in its neutral
variant, and future UX and testing must keep that explicit.

## Development direction

1. Establish the remaining job and LUT-writer boundaries without inheriting
   cross-model offsets.
2. Define what neutral output means and evaluate it separately from stock Color
   Creator.
3. Investigate editable recipes and selector behavior with an explicit
   return-to-stock path.
4. Consider Caduceus preset compatibility only after documenting the format and
   the target camera's processing constraints.

These are research milestones, not product features. Avoiding early menu changes
reduces scope but does not make a pipeline change safe.

## Investigate next

Look for COLOR versus Color Creator terminology, sector permutations,
capture-job sizes, dirty flags, native tone stages, recipe conventions, and the
difference between a CPU-side parameter record and rendered output. The Caduceus
projects and related firmware analysis are the starting points for prior art; a
working port on another camera is not evidence that an untested OM-5 port works.
