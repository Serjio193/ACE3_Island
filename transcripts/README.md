# Video transcript index

This directory contains cleaned technical transcripts from the research chat.

Non-technical material is removed: greetings, repeated filler, music, camera/OBS chatter, soldering dead time, unrelated interruptions, and presentation narration. Technical observations, experiment sequence, timestamps for important events, uncertainties, and conclusions are retained.

| Video | File | Description | Status |
|---:|---|---|---|
| 00 | [00-course-announcement.md](00-course-announcement.md) | Technical scope of the A3/ACE3 serialization/replacement course and stated workaround | Cleaned |
| 61 | [61-a3-replacement-series-introduction.txt](61-a3-replacement-series-introduction.txt) | A3 replacement questions, SN/P variants, ROM transfer, touch, MacBook relevance | Cleaned |
| 62 | [62-rom-touch-and-portdfu.txt](62-rom-touch-and-portdfu.txt) | External ROM/application code, touch behavior, blank ROM, PortDFU, restore rebuilding firmware | Cleaned |
| 63 | [63-same-model-sn-swap.txt](63-same-model-sn-swap.txt) | Same-model SN A3 swap; PortDFU identity follows donor A3 while normal boot shows recipient identity | Cleaned |
| 64 | [64-cross-model-15-to-15plus.txt](64-cross-model-15-to-15plus.txt) | Cross-model A3 swap; temporary boot, checkpoint 131D/TSS refusal, 3uTools contrast | Cleaned |
| 65 | [65-p2027-vs-sn2027.txt](65-p2027-vs-sn2027.txt) | P-vs-SN experiment; Apple refusal, matching-ROM requirement, 3uTools result | Cleaned |
| 66 | [66-decrypted-unidentified-a3-chip.txt](66-decrypted-unidentified-a3-chip.txt) | “Decrypted” replacement; works with existing ROM, Apple restore succeeds, identity absent in forced DFU, MacVDM workaround | Cleaned |

## Editorial rule

Keep:
- hardware configuration,
- exact observed states,
- PortDFU/recovery/normal-mode identity differences,
- restore/TSS errors,
- firmware/ROM behavior,
- hypotheses clearly marked as hypotheses,
- practical conclusions relevant to ACE3 replacement.

Remove:
- greetings and farewells,
- “let me show you” / “we’ll see” filler,
- music/singing/noise markers,
- camera/OBS/computer complaints,
- soldering/reballing waiting time,
- customer-call interruptions,
- unrelated shopping or presentation chatter.
