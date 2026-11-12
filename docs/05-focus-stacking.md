# Focus Stacking

[← Aperture ring](04-aperture-ring.md) · [Overview](index.md) ·
[Next: Caduceus-style COLOR →](06-caduceus-color.md)

An eight-byte eligibility change was evaluated in **69 bounded CPU runs**. The
observational assertions passed, but complete qualification stays **negative**:
a stock interpolation path reaches an instruction the chosen simulator does not
implement, and the change also shifts lens-admission policy. There is no
qualified firmware or installation procedure here.

## Bracketing is not stacking

Focus Bracketing produces a sequence at different focus settings; in-camera
Focus Stacking also processes and combines one. Working AF is therefore not
evidence of unrestricted stacking compatibility. The two use different admission
gates.

| Landmark | Role |
|---|---|
| `0x606d5c50` | Focus Bracketing eligibility |
| `0x606d63e5` | Focus Stacking eligibility |
| `0x606d5ea9` | Shared focus-bracket calculation |
| `0x605a1a26` | Magnification-change interpolation |

These are analysis landmarks, not a protocol for driving every lens.

## Focus-breathing data gates stacking

Step computation draws on descriptor data including `LAF_TH_PLS` and
`LIFT_K_M`; the shared step route helps explain why bracketing can be available
while stacking is not. Stacking additionally depends on the `enable.mag_cng`
state at descriptor offset `0x1bc9f`, pointing at magnification-change data
(focus breathing) at offset `0x1d3f8`. Setting a table-presence flag without
providing a valid table would be unsafe; the evaluated candidate does not do
that.

## What the zero-table test proves — and does not

With the table deliberately unmapped and the `mag_cng` state absent, the stock
interpolator returns zero.

| Shown | Not shown |
|---|---|
| This path does not dereference the missing table | Downstream unit prescaling |
| The zero return is native, not fabricated | ISP registration tolerance, merging, output quality |

Earlier summaries that promoted the zero return into a demonstrated `1.0000x`
ISP result went beyond the executed evidence. Large focus breathing stays a
plausible optical concern, but no per-lens tolerance or merged JPEG was measured.

## The eligibility candidate and its policy shift

The change delegates stacking eligibility to the stock bracketing gate and
rejoins the existing path. It does not spoof maker identity, create RAM state,
or add a code cave. The policy differences are the point:

| Compared to original stacking | Effect of delegation |
|---|---|
| Panasonic-only exception | Not restricted to Panasonic; other admitted MFT AF fixtures also pass |
| Invalid `mag_cng = 2` | Can be admitted |
| Valid-table flag plus a bracketing exclusion | Can be rejected even where the original gate accepts it |

Delegation is therefore not a strict superset of original behavior; no claim
that it preserves every stock exclusion is justified.

## The simulator blocker

For populated synthetic tables, native fixed-point interpolation reaches
`udf14 64,d0` at `0x600ef261`. The strict GNU runner does not implement that
operation and stops. An independent emulator documents this exact form as an
int32-to-float32 conversion (round-to-nearest-even) — a useful model to verify,
not proof that the operation ran in this test path. The historical result is 69
runs, 10 unittest methods with no assertion failures, and a negative overall
qualification with one explicit blocker. No patch manifest was issued.

## Investigate next

Pursue the distinction between a bracketing gate and a stacking gate,
focus-breathing table producers, the signed/float conversion semantics, and
preservation of eligibility for already-supported lenses. Completing the software
path still leaves physical focus accuracy, frame alignment, aperture stability
across a series, capture sequencing, and image quality to settle separately. Any
interaction with the [aperture-ring experiment](04-aperture-ring.md) needs its
own combined-image qualification.
