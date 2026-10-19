<!--
SPDX-FileCopyrightText: 2026 the innova2 contributors

SPDX-License-Identifier: Apache-2.0
-->

# noddr r1 (route V r3x4) — release notes, 2026-10-18

Package `innova2-noddr` 202320.3.0-2, platform `innova2_innova2_noddr_202320_3`, interface UUID
`9975da591e3d49aab449ab760829ddd0`, ROM_TS 1792486800 (`0x6ad72d90`). First public release of the noddr shell. Package release 2 (2026-10-19) changes
only the documentation (these notes, EVIDENCE.md, the README); every image and kernel is the same as in 202320.3.0-1.

**What it is.** The inn2x XDMA shell line (fp8c lineage: ddr_health 2.0, flight recorder, thermal throttle, P2P with
the 2 MB NIC-visible PLRAM window, XRT host memory) with the DDR4 controller moved from the static region into the
reconfigurable partition ("route V"):

* every xclbin keeps DDR or prunes it; the link hook `prune_unused.tcl` also prunes unused PLRAM0/1/2, HOST and PEER
  paths, each independently, with the partition boundary unchanged (the hook's G-BND/G-ADDR/G-USE/G-PINS gates);
* per-card DDR profiles (8/16/32/64-bit on chosen byte lanes) for cards with damaged DDR;
* a power-on **boot RM** (DDR pruned: the DDR pins are parked) so that a card boots the same way whatever its DDR;
* static region interconnect on crossbars ("r3x4"): reconfigurable partition 54,380 SLICE / 435,040 LUT /
  840 BRAM36 / 80 URAM / 1,608 DSP, about 406.8K LUT (93.5%) and 32 URAM free for kernels;
* static timing WNS +0.060 ns (place ExtraTimingOpt, route AggressiveExplore); DATA_CLK 300 / KERNEL_CLK 500 MHz.

**Not included:** ERT and CU interrupts (inn2x r10 has them), EMU/HYBRID DDR modes.

**Reproducible.** The images were built by `innova2_noddr_shell_src`'s `repro/build_release.sh` (Docker, frozen
clock, deterministic RDTSC). The routed static region and the default partial bitstream are byte-identical to the
native build they were validated against; a second clean build (2026-10-18) reproduces the MCS pair and the .bit byte for byte
(so also the BOPE bins, which are the MCS payload), with the same INTERFACE_UUID, ROM_TS, VBNV, boot RM and
implementation directives. The boot xsabin and the test xclbins were not rebuilt.

**Validation:** on 2026-10-18, one card (MNV303212A-ADLT) in an HP Z440, Ubuntu 26.04, kernel 7.0, XRT 2.27
with the `tools/xrt/` patches: the User slot was written from inn2x r10 over PCIe and a raw read-back of both chips
matched the MCS pair byte for byte, Factory unchanged; after a cold cycle the card ran this shell (ROM_TS 0x6ad72d90,
Device Ready) with this release's boot xsabin and test xclbins. `xrt-smi validate -r all`: verify, dma (bank0: 6.5 GB/s
host to card, 6.6 GB/s card to host), mem-bw (bank0 DDR: 17.6 GB/s), p2p, pcie-link and sc-version PASSED; no `DMA
failed`; DDR4 at 2667 MT/s, ECC 0/0. `innova2-smi check` (innova2_py_app 1.3.24): no failures. The six examples of
`innova2_noddr_shell_examples` (lat_probe, fld_echo, memcached, ntp, iperf, hls4ml) pass on the same card.
