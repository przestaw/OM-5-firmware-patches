# Sources and References

[Overview](index.md) · [Research notice (EU)](disclaimer.md)

Research cut-off: **12 November 2026**.

This bibliography identifies prior work, useful project entry points, vendor
documentation, and community venues. It is not a dependency manifest or a
download/build procedure. No linked project's firmware, tools, images, or
resources are included in this repository.

The descriptions summarize the research record used when preparing these
notes. External pages were not comprehensively revalidated during the
creation of this documentation-only repository. Repository heads, releases,
licenses, and support claims can change.

## Firmware analysis and emulation

| Project | Relevant contribution or research direction | Important boundary |
|---|---|---|
| [fishcu/oly_fw_analysis](https://github.com/fishcu/oly_fw_analysis) | Olympus/OM container analysis, section mapping, HSL research, and bounded MN103 execution | Main, unmerged contributions, and release archives are different materials |
| [flatpanda/om-emu](https://github.com/flatpanda/om-emu) | MN103/AM33 instruction models, camera ports, GUI research, and emulator limitations | Modeled peripherals and a working UI do not prove physical camera behavior |
| [cmdada/em10-fw](https://github.com/cmdada/em10-fw) | Independent research into older Olympus firmware and subsystem organization | An earlier body's offsets and architecture are not an OM-5 port |

For the [architecture chapter](01-architecture-and-evidence.md), look for
container formats, loader descriptors, compressed sections, memory maps,
instruction-set coverage, provenance, and explicit emulator boundaries.
Finding a tool is not enough: its revision and declared assumptions matter.

## Aperture-ring prior work and hardware reports

| Source | Relevant contribution or report | Important boundary |
|---|---|---|
| [fishcu/oly_fw_analysis#4](https://github.com/fishcu/oly_fw_analysis/pull/4) | Separate PEN-F aperture-ring contribution, with implementation discussion and reported lens tests | Must not be conflated with an analysis of om-c found elsewhere in fishcu's project |
| [azqeurio/om-c-firmware](https://github.com/azqeurio/om-c-firmware) and [release history](https://github.com/azqeurio/om-c-firmware/releases) | Independent multi-body firmware-modification project used as comparative material | Reading a distributed application's code does not establish a license to redistribute its payloads |
| [azqeurio/om-c-firmware#9](https://github.com/azqeurio/om-c-firmware/issues/9) | OM-5 user report mentioning functioning ring recognition and a reboot associated with a different feature in the tested third-party builds | A user report does not identify or validate our own experimental binary |

For the [aperture-ring chapter](04-aperture-ring.md), investigate optional
status requests, official aperture bounds, mode restrictions, session resets,
and which exact camera/lens/firmware combination was tested.

## COLOR and Caduceus

| Project | Why it matters |
|---|---|
| [cakelesscoder/caduceus-ep7](https://github.com/cakelesscoder/caduceus-ep7) | Foundational Caduceus COLOR/HSLFX work, aperture-ring research, and experience porting to Olympus bodies |
| [androidfanman/caduceus-om3](https://github.com/androidfanman/caduceus-om3) | Development of the concept on another OM body; useful structural and preset references |
| [Caduceus Control Panel](https://cakelesscoder.github.io/caduceus-ep7/) | An ecosystem reference for look editing and interchange, not an interface implemented by our prototype |

The planned OM-5 work explicitly credits Caduceus as groundwork.
This publication is independent and does not imply participation, support,
or endorsement by the Caduceus authors.

For the [COLOR chapter](06-caduceus-color.md), look for sector ordering,
picture-mode selection, job layouts, native table builders, recipe semantics,
and the distinction between parameter generation and rendered output.

## Lumix and stabilization research

| Project | Relevant contribution | Important boundary |
|---|---|---|
| [flickering-star/lumix-firmware-research](https://github.com/flickering-star/lumix-firmware-research) | Panasonic update formats, decryption research, and GM1/GM5/GX9-related notes | Does not supply a verified implementation of Panasonic's IBIS/OIS coordination; no cryptographic key values are reproduced here |
| [Siegfried82/lumix-s1m2-research](https://github.com/Siegfried82/lumix-s1m2-research) | Broader investigation of Lumix update and cryptographic architectures | Reference for understanding what material exists and its limits |

For [lens communication](02-lens-body-communication.md) and
[stabilization](03-ibis-and-ois.md), distinguish diagnostic labels from runtime
traffic, a no-IBIS body from an IBIS body, and update-file encryption from any
separate question about communication over the mount.

## Official manufacturer material

| Material | Scope covered | Important boundary |
|---|---|---|
| [Interchangeable lens for Micro Four Thirds: Dual I.S. / Dual I.S.2](https://av.jpn.support.panasonic.com/support//global/cs/dsc/connect/dual_is.html) | Distinguishes the GX8/GX80/GX85/GX9 group from bodies with Dual I.S. 2 compatibility entries (including G80/G81/G85, GH5, and G9) along with lens firmware requirements | Compatibility claim, not a specification of internal message formats |
| [LUMIX 5-axis Dual I.S. delivers powerful image stability](https://blogs.panasonic.com.au/consumer/lumix-5-axis-dual-s-delivers-powerful-image-stability/) | GX85 material explaining five-axis body stabilization working with two-axis lens stabilization | Helps distinguish axis count from the Dual I.S. 2 name |
| [PEN E-P7](https://explore.omsystem.com/c/en/e-p7) | Identifies the E-P7 model and its color-profile and stabilization features | Not evidence that the proposed FLOW-style rotation effect exists or that Panasonic OIS interoperates with OM body correction |

## Reference snapshots

The original research record identifies these revisions. They are provenance
markers, not a statement that these are the latest versions or a promise of
reproducibility from this Markdown repository.

| Reference | Revision |
|---|---|
| fishcu/oly_fw_analysis main snapshot | `a61498eecba1125010956864acbefaa1e4ff5f9e` |
| Separate PEN-F ring contribution | `238708a503bccafe8c85cb56face53c485e79263` |
| Caduceus E-P7 local comparison | `a7a49a94595fcb15ebb89f46b346e02d9846450f` |
| Caduceus OM-3 local comparison | `ed0284ce7abcab40f7024604887ee8bcc864f63e` |
| OM-Emu comparison | `96bf75625601785eb54ec26f33770beff4cb7ac2` |
| Lumix firmware research | `9e363e26400889637d8b7383dad9a63b7b65b891` |
| Lumix S1M2 research | `8f625cfabb31e8a71177b86e2b63a69c686de5e8` |
| E-M10 research | `6adb3bef36239859ae3fcd94b0dbb7db6de6ffa1` |
| om-c comparative release | `dbv0.4.0`; its repository and release application are distinct sources |

For the [stacking blocker](05-focus-stacking.md#the-simulator-blocker),
OM-Emu's documentation of the exact `udf14` form is a specific place to look.
An alternative model still requires independent verification before it can
support a new qualification claim.

## Forums and community venues

These are discovery and discussion venues, not independently verified evidence
for this publication's experiments:

| Venue | Typical material | Caution |
|---|---|---|
| [Micro Four Thirds User Forum](https://www.mu-43.com/forums/) | Lens/body combinations, stabilization behavior, and color recipes | Identify exact firmware versions and test conditions |
| [Reddit r/M43](https://www.reddit.com/r/M43/) | User discussion of community firmware projects | Separate first-hand hardware reports from second-hand summaries |
| [Personal View](https://www.personal-view.com/talks/) | Historical Panasonic research and PTool discussions | Older results should not be assumed to apply to later cameras |
| [fishcu issue tracker](https://github.com/fishcu/oly_fw_analysis/issues), [Caduceus E-P7 issue tracker](https://github.com/cakelesscoder/caduceus-ep7/issues), [om-c issue tracker](https://github.com/azqeurio/om-c-firmware/issues) | Project-specific reports, corrections, and stated test boundaries | Check binary identity if available before comparing behavior |

When assessing a report, identify its date, exact camera and lens firmware,
binary identity if available, test conditions, and whether it is first-hand.
No discussion posts, project comments, or requests to contributors were
published while preparing this repository.

## Attribution and source rights

The analysis acknowledges the above authors' work rather than treating linked
projects as interchangeable dependencies. In the inspected fishcu snapshot,
original contributions and GNU-based simulator components have different
licenses, and vendor firmware is excluded from those grants.
Other projects and unmerged contributions retain their own terms.

Neither a readable source file nor a public release automatically grants
permission to republish third-party material. This bibliography links to
sources instead of distributing their code or firmware.

## EU copyright framework

- [Directive 2009/24/EC: legal protection of computer programs](https://eur-lex.europa.eu/legal-content/EN/ALL/?uri=CELEX%3A32009L0024):
  Article 5(3) on observation, study and testing by an entitled user, and
  Article 6 on decompilation indispensable for interoperability and limits
  on use and disclosure of the resulting information.
- [Directive 2001/29/EC: consolidated text](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX%3A02001L0029-20190606):
  Article 5(3)(d) on quotation for purposes such as criticism or review,
  and Article 5(5) on limits to exceptions.

These EU references must be considered with the applicable Member State's
implementing legislation and the actual circumstances. The
[disclaimer](disclaimer.md) explains why they are not a blanket authorization
for firmware copying, decompilation, publication or circumvention.
