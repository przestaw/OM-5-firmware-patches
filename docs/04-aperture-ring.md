# Electronic Aperture Ring

[← Stabilization](03-ibis-and-ois.md) · [Overview](index.md) ·
[Next: Focus stacking →](05-focus-stacking.md)

The OM-5 1.2 aperture-ring experiment has a complete supported software route
and a historical result of **6,837 bounded CPU runs with no unexpected
failures**. That is software qualification within declared bounds — not physical
lens compatibility and not approval to install firmware.

## Intended behavior

The module requests an electronic ring value from a capable native MFT lens,
parses the extra word, and uses it when choosing aperture in still A/M modes.

| Condition | Behavior |
|---|---|
| Capable lens, A or M (or Custom resolving to A/M) | Ring value is used |
| Unsupported mode, or ring value unavailable | Body setting is kept |
| Ring at A/auto sentinel `0x8000` | Body control is restored |

It is capability-based, not a claim that any named Panasonic, Leica, or DJI lens
was tested. None was tested physically with this independently written module.

## Request and parser integration

Only `C1 80` and `81 E3` are extended; `C1 00` is not, and requests carrying the
IS-extra option are left outside ring extension. The parser checks the required
response length before consuming the extended reply, allows additional trailing
bytes, and the dispatcher tests stop before the stock buffer-release service —
so they do not cover the full task lifecycle.

## Native modes and official aperture limits

The module calls the camera's own getters instead of a cross-model lookup table.

| Landmark | Role |
|---|---|
| `0x602d04e6` | Native mode resolution |
| `0x6023b02f` | Custom-mode information |
| `0x60214d18` | Effective/official aperture triple |
| `0x60464af3` | Selected-aperture integration point |

The triple is effective value, official minimum, official maximum. Signed ring
Av is clamped against the official minimum and maximum, not the effective field.
In one controlled test, raw `0x0187`, official minimum `0x0188`, and effective
`0x0194` resolve to the official minimum, preserving the intended nominal bound;
native zoom/teleconverter handling is retained. These are software values, not
measurements of iris position or transmission.

## State and footprint

Identity reception and transport errors invalidate cached ring state. Because the
six-byte identity is not shown to be a per-unit serial, session resets remain
necessary even for same-model lens changes. The experiment is seven change
regions and a 1,304-byte payload (including unused test entry points) at
`0x6023e200`–`0x6023e718`, below the stock-loaded boundary and needing no loader
edit. Its 16-byte cache sits at `0xa25988ac`; the placement fits the layout, but
exclusive runtime ownership against unknown interrupt/DMA writers is unproven.

## What 6,837 runs cover — and do not

| Covered within bounds | Not covered |
|---|---|
| Core gate/request/response/selection/reset paths | Physical aperture actuation |
| The complete examined stock parser | Live-view and DOF-preview behavior, exposure accuracy |
| Dispatcher blocks, native getters, official bounds, request formatting | EXIF, boot, recovery, scheduling, concurrency |
| Both outcomes of all 54 core conditional branches | Full firmware branch coverage; a combined ring/stacking/COLOR image |

Property-storage lookup and timing boundaries use explicit fixtures.

## Investigate next

The behavior rests on a few ideas another researcher can pursue from a clean
start: an optional status word, official versus effective Av, native mode
accessors, a parser-length contract, displaced-instruction ABI, and lens-session
invalidation. Independent descriptions of a PEN-F aperture-ring effort and of
other OM/Olympus modifications exist and report their own hardware tests; those
results belong to that work and must not be attributed to this OM-5 module. This
repository contains no patch code or application steps.
