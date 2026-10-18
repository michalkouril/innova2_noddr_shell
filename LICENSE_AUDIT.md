# Licence audit — innova2_noddr_shell (2026-10-18, noddr r1)

Every file in this repository, classified by provenance. Classes and what they mean are at the end.

**Provenance notes:** the flash images (MCS and BOPE per-chip .bin), bitstream, boot xsabins and validate xclbins are the noddr r1 shell (route V r3x4, reproducible build 2026-10-17): Vivado output containing AMD IP cores (our own PF demux; our IP cores carry VLNV vendor `innova2`). This shell has no processor and no firmware. `bandwidth.xclbin`'s kernel source is Xilinx's (Apache-2.0). `tools/xrt/*.patch` are our changes to XRT's GPL-2.0 xocl driver (the same files as inn2x r10's package), offered as GPL-2.0-only; the package ships the GPL text.

| file | class | licence evidence in the file |
|---|---|---|
| `.gitignore` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `LICENSE` | Licence text | Apache License; Apache License to your work, attac; Apache License to your work.; Copyrigh |
| `LICENSES/Apache-2.0.txt` | Licence text | Apache License; Apache License to your work, attac; Apache License to your work.; Copyrigh |
| `LICENSES/GPL-2.0-only.txt` | Licence text | Copyright (C) 1989, 1991 Free Software Foundation, Inc.,; GNU GENERAL PUBLIC LICENSE; copy |
| `LICENSES/LicenseRef-AMD-IP.txt` | Licence text | — |
| `LICENSE_AUDIT.md` | Ours | Apache License to your work.; Copy; Apache License; Apa /; Apache License; Apache License  |
| `NOTICE` | Ours | Copyright (C) 2019-2021 Xilinx, Inc., Apache-2.0.; Copyright 2026 the innova2 contributors |
| `README.md` | Ours | — |
| `REUSE.toml` | Ours | COPYRIGHT", "root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/LICENSE", "root/o; |
| `packaging/IDENTITY.sh` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `packaging/VERSION` | Ours | — |
| `packaging/build_packages.sh` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `packaging/install_from_tarball.sh` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/lib/firmware/xilinx/10ee-6a9f-4351-000000006ad72d90.xsabin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/10ee-6aa0-4351-000000006ad72d90.xsabin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/10ee-788f-4351-000000006ad72d90.xsabin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/10ee-7890-4351-000000006ad72d90.xsabin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3.bit` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_bope_primary.bin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_bope_primary.prm` | Ours, tool-generated | — |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_bope_secondary.bin` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_bope_secondary.prm` | Ours, tool-generated | — |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_primary.mcs` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_primary.prm` | Ours, tool-generated | — |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_secondary.mcs` | Vivado-built binary containing AMD IP | binary |
| `root/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_secondary.prm` | Ours, tool-generated | — |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/doc/EVIDENCE.md` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/doc/RELEASE_noddr_r1.md` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/COPYRIGHT` | Ours | Apache License, Version 2.0 (see L; Copyright (C) 2019-2021 Xilinx, Inc.,; Copyright 2026  |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/GPL-2.0-only.txt` | Licence text | Copyright (C) 1989, 1991 Free Software Foundation, Inc.,; GNU GENERAL PUBLIC LICENSE; copy |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/LICENSE` | Ours | Apache License; Apache License to your work, attac; Apache License to your work.; Copyrigh |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/LicenseRef-AMD-IP.txt` | Licence text | — |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/test/bandwidth.xclbin` | Vivado-built binary containing AMD IP | binary |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/test/platform.json` | Ours, tool-generated | — |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/test/verify.xclbin` | Vivado-built binary containing AMD IP | binary |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/test/verify_plram.xclbin` | Vivado-built binary containing AMD IP | binary |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/innova2_noddr_flash_user.sh` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/qspi_scan.py` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xbflash.probe` | Binary built from Xilinx Apache-2.0 source | binary |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xrt/README.md` | Ours | — |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xrt/innova2_devices.patch` | Ours, a patch to XRT's GPL-2.0 xocl driver | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: GPL-2.0-only |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xrt/innova2_host_retag.sh` | Ours | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: Apache-2.0 |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xrt/innova2_p2p_mmap_xocl_drm.patch` | Ours, a patch to XRT's GPL-2.0 xocl driver | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: GPL-2.0-only |
| `root/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/xrt/xocl_dev_minor_6_18.patch` | Ours, a patch to XRT's GPL-2.0 xocl driver | CopyrightText: 2026 the innova2 contributors; SPDX-License-Identifier: GPL-2.0-only |
| `root/usr/bin/innova2_noddr_flash_user` | Ours | symlink |
| `root/usr/share/doc/innova2-noddr/copyright` | Ours | COPYRIGHT; Copyright (C) 2019-2021 Xilinx, Inc. (Apache-2.0); tools/xbflash.probe built; C |

## Classes

* **Ours** (19 files): Written in this project. Apache-2.0, (c) 2026 the innova2 contributors (SPDX header or REUSE.toml).
* **Licence text** (6 files): Verbatim licence text (SPDX licence list, or our LicenseRef explanation). Not a work of ours to license.
* **Vivado-built binary containing AMD IP** (12 files): Bitstreams/images/xclbins/xsabins synthesised by Vivado: contain AMD IP cores (XDMA, MIG, SmartConnect, HWICAP, ...) under AMD's IP licence terms. Normal for Vitis platforms, but those terms apply.
* **Ours, tool-generated** (5 files): Produced by Vivado/our scripts from our own design or measurements; no third-party text. Apache-2.0 via REUSE.toml.
* **Binary built from Xilinx Apache-2.0 source** (1 files): xbflash.probe: XRT xbflash.qspi (Apache-2.0) + our probe hooks. Ship with the Apache licence and a modification notice.
* **Ours, a patch to XRT's GPL-2.0 xocl driver** (3 files): Our changes to XRT's xocl driver (GPL-2.0; devices.h is Apache-2.0 OR GPL-2.0); offered as GPL-2.0-only (SPDX header).

Local-path check (`/home`, `/nobackup`, `/data1`, `/tmp` in text files, xclbins, xsabins, .prm): none found.
