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
IS-extra option (`0x08`) are left outside ring extension. The parser checks the
required response length before consuming the extended reply, allows additional
trailing bytes, and rejects short extended replies with `0x6507`
(while connection loss preserves `0x6407`). The dispatcher tests stop before the
stock buffer-release service (`0x602273d3`) — so they do not cover the full task
lifecycle.

| Stock integration landmark | Role in the OM-5 1.2 route |
|---|---|
| `0x6021c80d` | Lens identity reception — invalidates cached ring state on mount or identity update |
| `0x6021f6ca` | Status parser entry preflight — verifies minimum RX length before any record write |
| `0x6021f797` | Mid-parser point after `DIS_FCENC` — consumes optional 16-bit ring word (`+2` cursor advance) and replays status flags |
| `0x60226ea6` | Request formatter before RX allocator call — sets `req[21] \|= 0x01` on eligible `C1 80` / `81 E3` polls (`lens_index == 0`, no `IS-extra`) |
| `0x60226edb` | Response dispatcher error path — invalidates ring cache on transport failure while preserving original error status |

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
`[0x6023e200, 0x6023e718)` (exclusive end), below the stock-loaded boundary
and needing no loader edit. Its 16-byte cache sits at `0xa25988ac`
(inside the alignment pad below primary
descriptor `*0xa26824e0 == 0xa25988c0`, storing a `"RING"` tag, 6-byte `L_ID`
copy, `u16` raw value at `0xa25988b6`, and `u32` validity flag at `0xa25988b8`);
the placement fits the layout, but exclusive runtime ownership against unknown
interrupt/DMA writers is unproven.

## What 6,837 runs cover — and do not

| Covered within bounds | Not covered |
|---|---|
| Core gate/request/response/selection/reset paths | Physical aperture actuation |
| The complete examined stock parser | Live-view and DOF-preview behavior, exposure accuracy |
| Dispatcher blocks, native getters, official bounds, request formatting | EXIF, boot, recovery, scheduling, concurrency |
| Both outcomes of all 54 core conditional branches | Full firmware branch coverage; a combined ring/stacking/COLOR image |

Property-storage lookup (`0x6005d9de`, `0x602f17a0`) and the timer counter
(`0x9890014c`) use explicit fixtures. For a representative comparison, use a
ready/connected native-MFT descriptor, matching six-byte identity and capability,
then compare stock without the extra word against the candidate with it.
The descriptor/global outputs should agree, apart from the candidate's ring
cache; changing the response length below the scoped minimum must give
`0x6507` and invalidate that cache. Native mode and bounds tests need their own
declared property fixtures, not getters replaced with unconditional success.

## Investigate next

The behavior rests on a few ideas another researcher can pursue from a clean
start: an optional status word, official versus effective Av, native mode
accessors, a parser-length contract, displaced-instruction ABI, and lens-session
invalidation. Independent descriptions of a PEN-F aperture-ring effort and of
other OM/Olympus modifications exist and report their own hardware tests; those
results belong to that work and must not be attributed to this OM-5 module. This
repository contains no patch code or application steps.
