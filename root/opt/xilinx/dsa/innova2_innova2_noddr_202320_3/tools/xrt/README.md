# XRT 2.27 patches for the Innova-2 noddr shell

The shell runs on stock XRT 2.27. These patches add features; they are the same files the inn2x r10 package
(`innova2-xdma`) ships, so a host that already applied them needs nothing more.

| patch | needed for |
|---|---|
| `innova2_devices.patch` | **XRT host memory** (`sp=...:host`/`HOST` banks). xocl matches the card by its VBNV; the patch's entry list includes `innova2_innova2_noddr_202320_3`. Without it, host-memory xclbins fail to load with `No such device` although `xrt-smi configure --host-mem enable` reports success. |
| `xocl_dev_minor_6_18.patch` | a **second card** on kernel >= 6.18 |
| `innova2_p2p_mmap_xocl_drm.patch` | **CPU access to P2P buffers** on kernel >= 6.17 |

Apply to the DKMS tree and rebuild (keep a copy of the original tree; reboot gracefully, do not `rmmod xocl`):

    sudo cp -a /usr/src/xrt-2.27.0 /usr/src/xrt-2.27.0.orig-innova2
    sudo patch -p1 -d /usr/src/xrt-2.27.0 < innova2_devices.patch          # and the others you need
    sudo dkms remove xrt/2.27.0 --all
    sudo dkms build xrt/2.27.0 -k $(uname -r) && sudo dkms install xrt/2.27.0 -k $(uname -r)
    sudo reboot

Check after the reboot: `strings /lib/modules/$(uname -r)/updates/dkms/xocl.ko | grep -c '^innova2_innova2_noddr_202320_3$'`
prints 1, and `/sys/bus/pci/devices/<user BDF>/` lists an `address_translator`.

**Host-memory tag case.** Link with `sp=<cu>.<port>:host` against the dev platform (`hw_dev.xsa`, which renames the
tag) or `:HOST` against a full platform from `innova2_noddr_shell_src`'s `kernels` stage; then retag the xclbin with
`innova2_host_retag.sh in.xclbin out.xclbin` (xocl recognises the bank only as `HOST[0]`). Enable with
`xrt-smi configure --host-mem enable --size 1G` (this unloads the current xclbin).
