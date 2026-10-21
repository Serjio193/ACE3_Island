# 820-02935 / A2991 — SPI defect, PortDFU and personalization

Recorded: 2026-10-21 (Europe/Kyiv).
Device: MacBook Pro 16-inch A2991, Mac15,9 / J516c / T6031.

## Evidence and scope

This case combines the repairer's physical observations and SPI comparison report with read-only IORegistry captures collected on 2026-10-21. Physical repair steps were reported by the repairer; the SPI binary dumps and a byte-level comparison are not included here. Do not treat this note as an independently reproduced repair procedure.

## Keep the two issues separate

1. The original problem was personalization failure after ACE3 replacement.
2. A separate SPI defect arose during the attempts to resolve personalization. It left both LEFT ACE3 controllers in ACE PortDFU.
3. The repairer reports a point where Recovery was completely erased. Following a subsequent DFU restoration, the personalization error stopped occurring **while the SPI defect was still present**.
4. Repairing the SPI boot area subsequently allowed a controller to leave ACE PortDFU.

Therefore, the SPI repair does not explain the earlier disappearance of the personalization error. The precise state changed by that successful DFU restoration has not been identified in a complete before/after record.

## Board positions and physical symptoms (repairer report)

| Position | ACE3 | SPI memory | Observed condition before SPI repair |
|---|---|---|---|
| UPC0 | UF400 | UF260 | LEFT, ACE PortDFU |
| UPC1 | UF500 | UF250 | LEFT, ACE PortDFU |
| UPC2 | UG400 | UG260 | RIGHT, working |

Apple Configurator detected ACE PortDFU via both LEFT ports, but Mac restoration did not complete in the affected state. ACE3 supplies and CC signals were checked; no significant difference was found in the main supplies and shared SPMI/I2C connections.

### Charging observation

On the affected LEFT ports in **ACE PortDFU**, the internal VBUS → PPHV path did not open and USB-PD did not negotiate 20 V. Those ports did not provide normal USB-C charging in this observed condition.

After restoring the UF260 boot area, its controller left PortDFU, board consumption rose to approximately 300 mA, and 20 V negotiation through MagSafe was observed.

This is a case-specific ACE PortDFU observation. It is not a claim that AP/system DFU always disables charging, that MagSafe can never charge during DFU, or that every ACE PortDFU implementation behaves identically. The 300 mA figure lacks a recorded measurement voltage/setup and must not be converted into a power estimate.

## SPI findings (repairer report)

SPI dumps were read and labelled by schematic position. Boot-area records present in the working UG260 and MagSafe memory dumps were absent in UF260 and UF250. The main ACE3 firmware regions nevertheless had valid CRCs.

Restoring the UF260 boot area changed the controller from ACE PortDFU to a working runtime state. Valid CRCs in the main firmware regions did not exclude a boot-area defect.

The defect appeared during attempts involving a phone DFU cable. The repairer's proposed mechanism is a change to ACE3 boot code/data in SPI. The actor, exact write/erase transaction and whether records were overwritten, erased or otherwise corrupted have not been captured. Do not present cable-induced code writing as proven.

## Runtime USB-C mapping and version — directly captured

On macOS 27.0.1 / 26A434, IORegistry exposed:

| Physical location property | Node / SPMI USID | Runtime UUID | Boot mode | Version |
|---|---|---|---|---|
| left-back | hpm0 / 0xC | 4FB0B603-5E12-175E-CCBD-C02CDE2A0A40 | APP | 0x00208700 |
| left-front | hpm1 / 0xA | CE6CB703-5027-475D-6CC0-CD6EED27BD20 | APP | 0x00208700 |
| right | hpm2 / 0x8 | 93741D00-9013-D7A7-E845-58EAA8CB85F4 | APP | 0x00208700 |

These locations are explicit IORegistry `port-location` properties, not an inference from numeric order. `HPM Mode Register on Boot` records the boot mode, not a continuously sampled current-mode register. All three HAL entries reported `I2C Read Failed = No`, Device ID 0x02012024, Revision ID 0xA1 and Vendor ID 0x28.

A separate MagSafe node hpm5 / 0x6 was also present. Do not count it as a fourth USB-C receptacle.

## RIGHT PortDFU identity — directly captured

The operator then initiated DFU and used a second Mac as the USB host. Reading existing host IORegistry USB properties returned Apple Device (Port DFU Mode), VID:PID **05ac:f014**, with this exact serial:

```text
SDOM:00 CPID:0022 BDID:0000000000000000 ECID:A7D71390001D7493 CPFM:00 SCEP:00 CPRV:0A1 PREV:00 SYSI:0000000000000000
```

Its ECID corresponds to the first eight runtime UUID bytes in reverse order:

```text
RIGHT UUID first 8 bytes: 93 74 1D 00 90 13 D7 A7
ECID:                    A7 D7 13 90 00 1D 74 93
```

The same representation relationship is observed for the two LEFT UUIDs and their previously captured ECIDs. This links this PortDFU response to the RIGHT runtime entry.

The descriptor read did not open an irecovery session, send a DFU download, reset the device or issue a configuration/interface command. No firmware, SPI or OTP/eFuse writes were performed during this diagnostic collection.

### Earlier blank-identity example is another physical chip

An earlier experiment captured:

```text
SDOM:00 CPID:0022 BDID:0000000000000000 ECID:8FC93D86001D7588 CPFM:00 SCEP:00 CPRV:0A1 PREV:00 SYSI:0000000000000000
```

The repairer clarified that a chip of this kind was temporarily installed at a LEFT position. This is not the current RIGHT ECID. These records demonstrate matching zero-valued fields on different chips; they do not constitute a same-chip identity before/after comparison.

## Conclusions we can use

- In this Mac, a RIGHT controller can run APP firmware and function while its PortDFU descriptor reports BDID/PREV/SDOM/CPFM/SYSI as zero.
- These zero-valued descriptor fields alone are insufficient evidence of a faulty controller or the cause of a personalization failure.
- Runtime firmware/version/UUID must not be substituted for a direct PortDFU identity read.
- A SPI boot-area problem can coexist with valid CRCs in the main firmware regions and with ACE PortDFU USB enumeration.
- No normal charging through the affected LEFT USB-C ports was observed in their ACE PortDFU state.
- Disappearance of the personalization error preceded SPI repair according to the repairer. The two events must remain separate in future analysis.

## Open hypothesis: internal personalization state

The repairer suspects that DFU restoration refreshed internal service/personalization data to accept the replacement ACE3 ECID. Here "internal data" means platform-managed data that we cannot directly control, not a claim of a conventional PC BIOS.

This remains a hypothesis. No location, persistence mechanism, ECID binding record or exact responsible restore stage has been identified. Repair of Recovery/firmware state without an explicit ECID pairing is also consistent with the observations. Zero-valued PortDFU fields neither prove nor disprove such external state. This case does not establish physical OTP/eFuse contents or successful factory provisioning.

For future comparable cases, preserve full restore logs, identities for each physical position, OS/Recovery versions, and labelled SPI dumps before and after each intervention. Do not retrospectively assign an unrecorded change to a specific step.

## Capture excerpts

See [IORegistry excerpts](ioreg-excerpts.txt) for the captured runtime properties and subsequent PortDFU serial. Unrelated host/account properties are omitted; complete captures remain in the local research archive.

