# Future Research

[← Caduceus-style COLOR](06-caduceus-color.md) · [Overview](index.md)

Intentional sensor rotation and Panasonic OIS with body-side roll correction are
separate research proposals. Neither is implemented or qualified here. The notes
below identify missing knowledge; they are not procedures for issuing actuator
commands, changing correction masks, or installing firmware.

## Intentional sensor rotation

A controlled sensor trajectory during exposure could be studied as a creative
effect, distinct from compensating accidental roll and not assumed to use the
same command interface. The term FLOW is only a working description; the review
found no primary source establishing it as an existing E-P7 or E-PL7
modification, and Caduceus's E-P7 target must not be confused with the
differently named E-PL7. Diagnostic drive names in OM firmware are leads, not a
safe angle, speed, current, or duration, and an axis-enable mask is not
necessarily a target-position or trajectory command.

| Must be established before any implementation |
|---|
| The actual controller command interface and receiver |
| Physical travel and actuator operating limits |
| Recentring and power-state behavior |
| Safe handling of exposure cancellation, errors, and interrupted communication |
| How the trajectory interacts with existing correction loops |

No motion sequences or actuator test instructions are supplied.

## Hybrid lens OIS and body correction

The narrower target is standalone lens pitch/yaw OIS with body-side roll
correction. It should not be called Panasonic Dual I.S. or OM Sync IS without
demonstrating the respective coordination contract. The
[stabilization analysis](03-ibis-and-ois.md) gives software leads — different
OIS classes, a restricted-mask helper, an exposure transport path — but does not
establish the physical effect of mask `4`.

| Still to resolve | Why it matters |
|---|---|
| The exposure-field producer | Determines what actually selects the correction path |
| CPU1/controller mapping and the mask receiver | Determines whether and how the mask reaches the servo |
| Lens-priority behavior across OIS lens types | Switched/switchless lenses, LV, capture, panning, cancellation, power transitions may differ |

Even a successful assignment of separate dimensions would need controlled
measurements to show correction is stable and beneficial; observing two enabled
mechanisms is not enough.

## Full cross-brand coordination

A more ambitious target would require Panasonic's own body-lens coordination —
units, timing, calibration, operating modes, error recovery — which shared MFT
status access does not supply. Useful evidence could include research on a
specific IBIS-capable Lumix body, independently understood lens behavior, or
properly interpreted communication measurements. The no-IBIS GM5 reference is no
substitute for a Dual I.S. implementation. Neither the absence of local plaintext
nor a marketing generation label proves the task impossible; they mark the limits
of current evidence.

## Investigate next

Look for the boundary between the application processor and the actuator
controller, lens-versus-body arbitration, operating-state transitions, and
physical calibration. Keep electrical observations, software models, and
image-quality measurements separate. Reference projects are attributed leads,
not promises about OM-5; this repository contains no tools and no instructions to
reproduce or deploy changes.
