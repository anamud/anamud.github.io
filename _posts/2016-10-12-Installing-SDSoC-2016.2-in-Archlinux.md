---
layout: post
title: Installing Xilinx SDSoC 2016.2 in Archlinux
---

I followed instructions from the Archlinux wiki pages for [ISE](https://wiki.archlinux.org/index.php/Xilinx_ISE_WebPACK) and [Vivado](https://wiki.archlinux.org/index.php/Xilinx_Vivado).

These are the extra steps I took to complete the installation.

- Copied custom platforms to `<install_root>/platforms`
- Installed cable drivers by running `<install_root>/Vivado/2016.2/data/xicom/cable_drivers/lin64/install_script/install_drivers`
- SDSoC crashed during startup. The coredump indicated problems with `#C [libgtk-3.so.0+0x180596]  gtk_css_provider_load_from_data+0x26`. I solved the crash by adding `export SWT_GTK3=0` to `<install_root>/.settings64-SDSoC.sh`
