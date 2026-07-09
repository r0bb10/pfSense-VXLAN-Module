# pfSense VXLAN Module

Backported VXLAN support as patch for pfSense CE 2.8.1.

## Tested On

```text
pfSense CE 2.8.1-RELEASE
FreeBSD 15.0-CURRENT RELENG_2_8_1 amd64
Kernel build: #21, Thu Aug 28 16:27:48 UTC 2025
```

## Install

1. Install the kernel module on pfSense.

Use the prebuilt copy from this repository:

```sh
fetch -o /boot/modules/if_vxlan.ko https://raw.githubusercontent.com/r0bb10/pfSense-VXLAN-Module/main/boot/modules/if_vxlan.ko
grep -qxF 'if_vxlan_load="YES"' /boot/loader.conf || echo 'if_vxlan_load="YES"' >> /boot/loader.conf
```

Or build a fresh `if_vxlan.ko` yourself using `BUILD.md`, then copy the built module to `/boot/modules/if_vxlan.ko` on pfSense.

2. Install the official `System_Patches` package in pfSense.

3. Go to `System > Patches > Add New Patch`.

4. Use these settings:

```text
Description: VXLAN Patch for pfSense CE 2.8.1
URL/Commit ID: https://raw.githubusercontent.com/r0bb10/pfSense-VXLAN-Module/main/patches/pfsense-vxlan-2.8.1-system-patches.patch
Path Strip Count: 2
Base Directory: /
Ignore Whitespace: checked
```

5. Click `Fetch`, then `Test`, then `Apply`.

6. Reboot pfSense.

7. Verify the module loaded:

```sh
kldstat | grep if_vxlan
```

After reboot, VXLAN pages should be available in the pfSense interface assignment UI.

## Notes

The patch only handles text/PHP changes. The kernel module is binary and must be installed separately, either from this repository or from a fresh build.

To build `if_vxlan.ko` yourself instead of using the prebuilt module, see `BUILD.md`.
