<!--
SPDX-FileCopyrightText: 2026 the innova2 contributors

SPDX-License-Identifier: Apache-2.0
-->

# Evidence — noddr r1 (route V r3x4)

**Build gates (reproducible build, 2026-10-17):** shell stage TIMING WNS=0.060 WHS=0.009; static locked (138,787
placed cells LOC/BEL-fixed); RP boundary 512-bit; route V post-route checks ok (DDR pins buffered in the RM, no HDPR
violations); boot RM: pr_verify -full_check COMPATIBLE with the routed static, WNS 0.060, no MIG/XPHY/PLL cell, DDR
reset/CKE parked low; BITCFG identical to the reference.

**Reproducibility:** `repro_check.py` against the native build: routed static .bit body, default-RM partial body,
INTERFACE_UUID, ROM_TS, VBNV and the place/route directives identical. Second reproducible build vs the release
(all images, MCS included): VALIDATION-PENDING.

**Hardware:** VALIDATION-PENDING.
