.. SPDX-License-Identifier: GPL-2.0-only

Running mmc-utils
-----------------
**Bash completion**
    Source ``completion/mmc`` to enable tab-completion for all subcommands and
    device paths. When installed via ``make install``, the file is placed in
    ``$(bashcompletiondir)`` (default: ``/usr/share/bash-completion/completions/mmc``)
    and loaded automatically by bash-completion.

**Name**
    mmc - a tool for configuring MMC storage devices
**Synopsis**
    ``mmc [options] [mmc-block-device]...``
**Description**
    *mmc-utils* is a single-threaded tool that will perform a specified type of mmc action as specified by the user.
    The typical use of mmc-utils is to access the mmc device either for configuring or reading its configuration registers.
**Options**
    ``help | --help | -h | (no arguments)``
        Shows the abbreviated help menu in the terminal.

**Commands**
    ``extcsd read <device>``
        Print extcsd data from <device>.

    ``extcsd write <offset> <value> <device>``
        Write <value> at offset <offset> to <device>'s extcsd.

    ``writeprotect boot get <device>``
        Print the boot partitions write protect status for <device>.

    ``writeprotect boot set [-p] <device> [<number>]``
        Set the boot partition write protect status for <device>.
        If <number> is passed (0 or 1), only protect that particular eMMC boot partition, otherwise protect both. It will be write-protected until the next boot.
        -p  Protect partition permanently instead. NOTE! -p is a one-time programmable (unreversible) change.

    ``writeprotect user get <device>``
        Print the user areas write protect configuration for <device>.

    ``writeprotect user set <type> <start block> <blocks> <device>``
        Set user area write protection.

    ``csd read  [-h] [-v] [-b bus_type] [-r register]  <device path>``
        Print CSD data from <device path>. The device path may be one of the following:
          - sysfs device directory  (/sys/devices/platform/fe310000.mmc/mmc_host/mmc0/mmc0:0001)
          - device node (/dev/mmcblkN)
          - sysfs block device entry (/sys/block/mmcblkN)
        /dev/ and /sys/block/ paths are resolved to the sysfs device
        directory automatically and the resolved path is printed.
        If [-b bus_type] is passed (mmc or sd) the [-r register] content must
        be passed as well, and no device path is required. Useful when the
        register value is known without access to the actual hardware.

        Example::

            $ mmc csd read /dev/mmcblk0
            sysfs: /sys/devices/platform/fe310000.mmc/mmc_host/mmc0/mmc0:0001
            CSD Register: 00000000...

    ``cid read <device path>``
        Print CID data from <device path>. The device path may be one of the following:
          - sysfs device directory  (/sys/devices/platform/fe310000.mmc/mmc_host/mmc0/mmc0:0001)
          - device node (/dev/mmcblkN)
          - sysfs block device entry (/sys/block/mmcblkN)
        /dev/ and /sys/block/ paths are resolved to the sysfs device
        directory automatically and the resolved path is printed.
        If [-b bus_type] is passed (mmc or sd) the [-r register] content must
        be passed as well, and no device path is required. Useful when the
        register value is known without access to the actual hardware.

        Example::

            $ mmc cid read /dev/mmcblk0
            sysfs: /sys/devices/platform/fe310000.mmc/mmc_host/mmc0/mmc0:0001
            Manufacturer ID: 0x15
            OEM ID: 0x0100
            Product name: MAG4FA
            Product revision: 1.0
            Serial number: 0x1a2b3c4d
            Manufacturing date: 01/2021

    ``scr read <device path>``
        Print SCR data from <device path>. The device path may be one of the following:
          - sysfs device directory  (/sys/devices/platform/fe320000.mmc/mmc_host/mmc1/mmc1:aaaa)
          - device node (/dev/mmcblkN)
          - sysfs block device entry (/sys/block/mmcblkN)
        /dev/ and /sys/block/ paths are resolved to the sysfs device
        directory automatically and the resolved path is printed.
        If [-b bus_type] is passed (mmc or sd) the [-r register] content must
        be passed as well, and no device path is required. Useful when the
        register value is known without access to the actual hardware.

        Example::

            $ mmc scr read /dev/mmcblk1
            sysfs: /sys/devices/platform/fe320000.mmc/mmc_host/mmc1/mmc1:aaaa
            SCR Register: 0235800000000000

    ``list``
        List all MMC/SD devices present on the system. Output is a table with
        columns: DEVICE (sysfs name), DEV (/dev path), TYPE (MMC or SD),
        MANUFACTURER, PRODUCT, REV, SERIAL, and DATE.

        Example::

            $ mmc list
            DEVICE          DEV            TYPE  MANUFACTURER         PRODUCT    REV   SERIAL      DATE
            mmc0:0001       /dev/mmcblk0   MMC   Samsung              MAG4FA     1.0   0x1a2b3c4d  2021-jan
            mmc1:aaaa       /dev/mmcblk1   SD    SanDisk              SP32G      8.0   0x5e6f7a8b  2020-mar

    ``ffu <image name> <device> [chunk-bytes]``
      Default mode.  Run Field Firmware Update with `<image name>` on `<device>`. `[chunk-bytes]` is optional and defaults to its max - 512k. Should be in decimal bytes and sector aligned.

    ``opt_ffu1 <image name> <device> [chunk-bytes]``
      Optional FFU mode 1, it's the same as 'ffu', but uses CMD23+CMD25 for repeated downloads and remains in FFU mode until completion.

    ``opt_ffu2 <image name> <device> [chunk-bytes]``
      Optional FFU mode 2, uses CMD25+CMD12 Open-ended Multiple-block write to download and remains in FFU mode until completion.

    ``opt_ffu3 <image name> <device> [chunk-bytes]``
      Optional FFU mode 3, uses CMD24 Single-block write for downloading, exiting FFU mode after each block written.

    ``opt_ffu4 <image name> <device> [chunk-bytes]``
      Optional FFU mode 4, uses CMD24 Single-block write for repeated downloads, remaining in FFU mode until completion.


    ``erase <type> <start address> <end address> <device>``
        Send Erase CMD38 with specific argument to the <device>. NOTE!: This will delete all user data in the specified region of the device. <type> must be one of: legacy, discard, secure-erase, secure-trim1, secure-trim2, or trim.

    ``gen_cmd read <device> [arg]``
        Send GEN_CMD (CMD56) to read vendor-specific format/meaning data from <device>. NOTE!: [arg] is optional and defaults to 0x1. If [arg] is specified, then [arg] must be a 32-bit hexadecimal number, prefixed with 0x/0X. And bit0 in [arg] must be 1.

    ``lock <parameter> <device> [password] [new_password]``
        Usage: mmc lock <s|c|l|u|e> <device> [password] [new_password]. <password> can be up to 16 character plaintext or hex string starting with 0x. s=set password, c=clear password, l=lock, sl=set password and lock, u=unlock, e=force erase.

    ``softreset <device>``
        Issues a CMD0 softreset, e.g., for testing if hardware reset for UHS works.

    ``preidle <device>``
        Issues a CMD0 GO_PRE_IDLE.

    ``boot_operation <boot_data_file> <device>``
        Does the alternative boot operation and writes the specified starting blocks of boot data into the requested file. Note some limitations: The boot operation must be configured, e.g., for legacy speed. The MMC must currently be running at the bus mode that is configured for the boot operation (HS200 and HS400 not supported at all). Only up to 512K bytes of boot data will be transferred. The MMC will perform a soft reset, if your system cannot handle that do not use the boot operation from mmc-utils.



    ``mmc rpmb write-block <rpmb device> <address> <256 byte data file> <key file>``
        Writes a block of data to the RPMB partition.

    ``mmc rpmb read-counter <rpmb device>``
        Reads the write counter from the RPMB partition.

    ``mmc rpmb read-block <rpmb device> <address> <blocks count> <output file> [key file]``
        Reads blocks of data from the RPMB partition.

    ``mmc rpmb secure-wp-mode-on <rpmb device> <key file>``
        Enable Secure Write Protection mode.

    ``mmc rpmb secure-wp-mode-off <rpmb device> <key file>``
        Disable Secure Write Protection mode = legacy mode.

    ``mmc rpmb secure-wp-disable <rpmb device> <key file>``
        Enable updating WP related EXT_CSD and CSD fields.
        Applicable only if secure write protect mode is enabled.

    ``mmc rpmb secure-wp-enable <rpmb device> <key file>``
        Disable updating WP related EXT_CSD and CSD fields.
        Applicable only if secure write protect mode is enabled.


    ``mmc cache flush <device>``
        Flush the eMMC cache for <device>.
        Applicable only if device version >= eMMC5.0.
