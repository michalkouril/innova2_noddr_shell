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
INTERFACE_UUID, ROM_TS, VBNV and the place/route directives identical. A second clean build (2026-10-18)
reproduces the MCS pair and the .bit byte for byte (so also the BOPE bins, which are the MCS payload), with the same
INTERFACE_UUID, ROM_TS, VBNV, boot RM and implementation directives. The boot xsabin and the test xclbins were not rebuilt.

**Hardware:** On 2026-10-18 one card (MNV303212A-ADLT) in an HP Z440, Ubuntu 26.04, kernel 7.0, XRT 2.27
with the `tools/xrt/` patches: the User slot was written from inn2x r10 over PCIe and a raw read-back of both chips
matched the MCS pair byte for byte, Factory unchanged; after a cold cycle the card ran this shell (ROM_TS 0x6ad72d90,
Device Ready) with this release's boot xsabin and test xclbins. `xrt-smi validate -r all`: verify, dma (bank0: 6.5 GB/s
host to card, 6.6 GB/s card to host), mem-bw (bank0 DDR: 17.6 GB/s), p2p, pcie-link and sc-version PASSED; no `DMA
failed`; DDR4 at 2667 MT/s, ECC 0/0. `innova2-smi check` (innova2_py_app 1.3.24): no failures. The six examples of
`innova2_noddr_shell_examples` (lat_probe, fld_echo, memcached, ntp, iperf, hls4ml) pass on the same card.
