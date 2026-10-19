# innova2_noddr_shell — run the Innova-2 as an XRT accelerator with DDR chosen per xclbin (noddr r1, 2026-10-18)

The minimal package: what a host needs to boot the card on the **noddr** ("route V") XRT shell and validate it.
Related repositories:

| repository | for |
|---|---|
| **innova2_noddr_shell** (this) | a card owner: flash the shell, install the host files, validate |
| `innova2_noddr_shell_src` | rebuilding the shell from source (`repro/build_release.sh`), bit-identical to these images |
| `innova2_noddr_shell_examples` | network examples on PLRAM rings, and the DDR fitness kit (which DDR lanes of *your* card work) |
| `innova2_py_app` | managing the card: burning flash, image select, JTAG grant, health; and `innova2-smi` |
| `innova2_shell` | the other shell, inn2x r10 (ERT, DDR in the static region); this package installs next to it |

| | |
|---|---|
| Shell | **noddr r1 (route V r3x4)**: the DDR4 memory controller (MIG) lives in the **reconfigurable partition**, so each xclbin either keeps DDR or prunes it and gets the area back. Built from `innova2_noddr_shell_src` by its reproducible build (`repro/build_release.sh`). See `RELEASE_noddr_r1.md` and `EVIDENCE.md` |
| Platform (VBNV) | **`innova2_innova2_noddr_202320_3`** |
| Interface UUID | `9975da591e3d49aab449ab760829ddd0` |
| ROM_TS | 1792486800 (`0x6ad72d90`), the key of the boot xsabin |
| Reconfigurable partition | 54,380 SLICE / 435,040 LUT / 840 BRAM36 / 80 URAM / 1,608 DSP; about 406.8K LUT (93.5%) and 32 URAM are left for kernels after the platform's own logic in the partition |
| Memory | bank0 DDR4-2666, 8 GB, 72-bit with ECC **when an xclbin keeps it**; per-card narrower DDR profiles (8/16/32/64-bit on the lanes that work, no ECC); PLRAM0/1/2 (128 KB / 128 KB / 1 MB); host RAM through XRT host-memory buffers (needs the `innova2_devices.patch` xocl patch). Every memory no kernel uses is pruned at link time |
| Clocks | DATA_CLK 300 MHz and KERNEL_CLK 500 MHz by default, both scaled by v++ to what the kernels meet; static region WNS +0.060 ns |
| Host link | PCIe Gen3 x8 via the card's ConnectX-5 switch |
| Validated board | MNV303212A-ADLT rev A7 |

**Not in this shell** (compared with inn2x r10): no ERT (XRT drives the CUs from the host), no CU completion
interrupts, no `emu`/`hybrid` DDR modes. The NIC-visible PLRAM window (2 MB, P2P BAR) is present.

Every file here belongs to this one build and they must be installed together. XRT identifies the shell only by its
feature-ROM timestamp. Link kernels against `innova2_noddr_shell_src`'s platform (rebuilt bit-identically), always with
the platform's link hook (below), and don't carry xclbins over from other shells. **Pre-release route V builds (r3,
r3x3) used the same ROM timestamp with a different static region: reflash such a card with this release before
loading these xclbins.**

`dpkg -V innova2-noddr` / `rpm -V innova2-noddr` (or the tarball's `install.sh --check`) tells you whether a host
still matches this package.

**Requirements:**
* XRT 2.27 (tested: master `1ea5176db` on Ubuntu 26.04, kernel 7.0). Stock XRT runs the shell; the xocl patches in
  `tools/xrt/` add host memory, a second card on kernel >= 6.18 and P2P buffers on kernel >= 6.17.
* Root.
* The `innova2_py_app` package (1.3.23 or later), for flashing from the Flex image and for `innova2-smi`.

## Install (release packages)

| system | command |
|---|---|
| Debian / Ubuntu | `sudo apt install ./innova2-noddr_202320.3.0-1_all.deb` |
| RHEL / Rocky / Fedora | `sudo dnf install ./innova2-noddr-202320.3.0-1.noarch.rpm` |
| anything else | `tar xzf innova2-noddr-202320.3.0-1.tar.gz && sudo innova2-noddr-202320.3.0-1/install.sh` (`--check` compares only) |

The package shares no file with `innova2-xdma` (inn2x r10): both can be installed, and the card's ROM timestamp
decides which boot xsabin xclmgmt loads. None of the packages flashes the card.

| installed path | what |
|---|---|
| `/lib/firmware/xilinx/10ee-{788f,7890,6a9f,6aa0}-4351-000000006ad72d90.xsabin` | boot firmware; xclmgmt loads it by the card's ROM timestamp |
| `/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_{primary,secondary}.mcs` (+ `.prm`) | the User-slot image, one file per QSPI chip, for flashing from a running XRT shell (`innova2_noddr_flash_user`) |
| `/lib/firmware/xilinx/innova2_innova2_noddr_202320_3_bope_{primary,secondary}.bin` (+ `.prm`) | the same image as per-chip binaries, for the Flex image's burn endpoint (`innova2_app -b`); byte-identical to the MCS contents |
| `/lib/firmware/xilinx/innova2_innova2_noddr_202320_3.bit` | the same design (static region + the power-on boot RM) as a bitstream, for a volatile JTAG load |
| `/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/test/` | `verify.xclbin` (keeps DDR), `bandwidth.xclbin` (keeps DDR) and `platform.json`, which `xrt-smi validate` needs; `verify_plram.xclbin` (DDR pruned) for cards whose DDR does not calibrate |
| `/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/tools/` | `innova2_noddr_flash_user.sh`, on `PATH` as `innova2_noddr_flash_user` (with its helpers `xbflash.probe` and `qspi_scan.py`); `xrt/` (the XRT 2.27 xocl patches, how to apply them, `innova2_host_retag.sh`) |
| `/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/license/` | LICENSE (Apache-2.0) / COPYRIGHT. Our work is Apache-2.0; the binaries also contain AMD IP cores under AMD terms (`LicenseRef-AMD-IP`) |
| `/opt/xilinx/dsa/innova2_innova2_noddr_202320_3/doc/` | this README, the release notes and the evidence summary. Not under `/usr/share/doc`, which minimized Ubuntu images exclude |

## Flash the card and validate

1. Install the package, above.
2. **Flash the User slot.** Which way depends on what the card is running:
   * **Card already running an innova2 XRT shell with both flash chips wired** (rbds, rbdm, inn2x, noddr): run
     `sudo innova2_noddr_flash_user` (`--card <mgmt BDF>` when the host has several cards). It probes both flash
     chips first (read-only) and refuses unless both answer with a Micron ID, scans the card's Factory region,
     writes the User partition with XRT's `xbflash.qspi`, and scans Factory again (it fails if any block changed).
   * **Card running the Flex image:** use the vendor burn flow with the BOPE images:
     ```
     cd /lib/firmware/xilinx
     sudo innova2_app -b innova2_innova2_noddr_202320_3_bope_primary.bin,0 \
                      -b innova2_innova2_noddr_202320_3_bope_secondary.bin,1
     ```
   * **Card running an old shell** (drp … rbdf, flash chip 1 unwired): use the Flex route. Never burn from those
     shells: they would write half an image.
3. **Select the User image and COLD cycle** (power off, then on). The FPGA reads flash only at power-on.
4. **Validate:** `xrt-smi validate -d <user BDF> -r all`. Then `innova2-smi check`: until the first xclbin load the
   partition holds the power-on **boot RM** (DDR pruned, `no xclbin (boot RM: PRUNED filler)` in `innova2-smi list`).
   On a card with bad DDR, `verify.xclbin` is refused (calibration timeout, -110) or fails validate's data checks,
   and the card stays healthy; load `verify_plram.xclbin` instead and use DDR-pruned or per-card-profile xclbins.

**Rollback:** flash the previous shell with its own package (for inn2x r10: `sudo innova2_flash_user` from
`innova2-xdma`) and cold cycle.

## DDR in the reconfigurable partition

Every link against this platform must pass the platform's link hook, `prune_unused.tcl` (in the platform's `hooks/`):

    v++ -l ... --advanced.param compiler.userPreSysLinkOverlayTcl=<platform>/hooks/prune_unused.tcl

It removes every memory path no kernel of the link uses: DDR (the MIG goes, the pins are parked), PLRAM0/1/2, and the
HOST and PEER paths. A pruned PLRAM reads 0 and drops writes; XRT never allocates in it. `INNOVA2_PRUNE` and
`INNOVA2_PRUNE_KEEP` (environment, read during the link) opt out, e.g. `INNOVA2_PRUNE_KEEP=PLRAM0` for a host-only
buffer no kernel touches.

* **KEPT DDR** (a kernel port on `bank0`): the full 72-bit ECC DDR4-2666 by default, with the CA pins at 60 ohm.
  `INNOVA2_DDR_VARIANT` / `INNOVA2_CA_DRIVE` select another speed or drive at link time.
* **Per-card DDR profile:** for a card where only some DDR byte lanes work, a profile file sets
  `::innova2_ddr_profile {name … lanes {5 6 7 8} variant dw8_833 ca RDRV_60_60}` and sources `prune_unused.tcl`;
  name the profile file instead of the hook. Widths 8/16/32/64, 1 GB per lane, no ECC. The DDR fitness kit in
  `innova2_noddr_shell_examples` finds the widest profile your card passes.
* **PRUNED DDR** (no kernel on `bank0`): no MIG; the host DDR window reads 0 / is fenced; the card's DDR is irrelevant.

A KEPT xclbin calibrates its DDR at load. If calibration fails, XRT refuses the xclbin with a calibration timeout
(-110) and the shell stays usable: the next load (any xclbin) reconfigures the partition. A calibrated DDR is not
proof of good DDR on a damaged card (a dead byte lane can still calibrate): use the fitness kit or `innova2-smi test
ddr-soak` before trusting it.

## Health: `innova2-smi`

`innova2-smi` (in `innova2_py_app`) reads the shell's `ddr_health` block: `list`, `status` (with route V's `loads`
counter and the loaded RM: KEPT / PRUNED / boot RM), `check` (passive; exit 0 OK / 1 WARN / 2 FAIL), `ddr`, `memory`
(PRUNED banks), `counters`, `recorder`, and active tests (`test ddr-soak`, `test pr-stress`, …).

## Things to know

* **XRT 2.27 patches (`tools/xrt/`)**: the same patches as inn2x r10; a host that has them needs nothing more.
* **Host-memory tag case:** `sp=...:host` against the dev platform (`hw_dev.xsa`), `sp=...:HOST` against a full
  platform from `innova2_noddr_shell_src`'s `kernels` stage; then retag (`tools/xrt/innova2_host_retag.sh`).
* **NIC-visible PLRAM (2 MB window)** is off at reset; a host program enables it through user-PF BAR0 + 0x102000. The
  window is 2 MB-aligned in the P2P BAR: PLRAM0 at +0, PLRAM1 at +128 KB, PLRAM2 (1 MB) at +1 MB; **+256 KB .. +1 MB
  is an unmapped hole that must not be touched.** A pruned PLRAM in the window reads 0.
* **PCI IDs:** mgmt `10ee:788f`, user `10ee:6a90`, subsystem `10ee:4351` are entries in stock XRT's device tables,
  used so that an unmodified XRT binds the card. This is not an AMD product and is not endorsed by AMD.
* **P2P: load an xclbin that keeps DDR first after every reset.** XRT's P2P slot table is fixed by the first xclbin
  loaded after a reset. If that one prunes DDR (for example `verify_plram.xclbin`), every later P2P buffer in `bank0`
  fails (`map P2P failed, ret = -2` in dmesg, `failed to allocate bo: Operation not permitted` in the application)
  until the next warm reboot. Load `verify.xclbin` first, then your xclbin. P2P itself is off after a cold boot:
  `sudo xrt-smi configure -d <user BDF> --p2p enable`, then warm reboot.
* **`xrt::bo::sync` hides DMA errors** (an upstream XRT bug): a DMA into pruned or fenced memory logs "DMA failed" in
  dmesg while the application sees success.
* **HP Z440:** after a PCIe change the BIOS stops at "press F1"; remote power control cannot always clear it.
* **Do not verify flash through `/dev/xfpga`**: chip 1 reads back as zeros. Trust a cold boot.

## Validation

VALIDATION-PENDING: filled in from the release's hardware runs.

## Repository layout and building the packages

```
root/         the payload, exactly as it lands on the host
packaging/    build_packages.sh (-> dist/*.deb *.rpm *.tar.gz + SHA256SUMS), install_from_tarball.sh,
              IDENTITY.sh (uuid / ROM_TS / VBNV), VERSION
```

`packaging/build_packages.sh` needs `dpkg-deb` and `rpmbuild`. It stages under `dist/` (git-ignored), never `/tmp`.
Set `MAINTAINER="Name <email>"` for the packages' maintainer field; the default is `Michal Kouril <xmkouril@gmail.com>`.
