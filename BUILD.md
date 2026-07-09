# Build the VXLAN Kernel Module

These steps are for users who want to build `if_vxlan.ko` themselves instead of using the prebuilt module shipped in this repository.

The recommended build method is the included GitHub Actions workflow. It runs on a FreeBSD 15 amd64 VM, builds `if_vxlan.ko` from upstream FreeBSD source, and uploads the module as a downloadable artifact.

## Tested Target

```text
pfSense CE 2.8.1-RELEASE
FreeBSD 15.0-CURRENT RELENG_2_8_1 amd64
Kernel build: #21, Thu Aug 28 16:27:48 UTC 2025
```

## Build With GitHub Actions

1. Fork this repository to your own GitHub account.

2. Open your fork in GitHub.

3. Go to the `Actions` tab.

4. If GitHub asks to enable workflows for the fork, click `I understand my workflows, go ahead and enable them`.

5. Select the `Build if_vxlan module` workflow.

6. Click `Run workflow`.

7. Keep the default branch selected, then click the green `Run workflow` button.

8. Wait for the workflow run to finish successfully.

9. Open the completed workflow run.

10. Download the artifact named `if_vxlan-freebsd15-amd64`.

11. Extract the downloaded ZIP file. It contains:

```text
if_vxlan.ko
```

## What the Workflow Builds

The workflow uses upstream FreeBSD source because the pfSense CE kernel source tree is not published in the same way as the pfSense PHP/UI sources. For this module, the `if_vxlan` source is not pfSense-specific, so it can be built from matching upstream FreeBSD 15 source instead.

The workflow currently builds from:

```text
FreeBSD branch: releng/15.0
FreeBSD commit: bf06074106cf80564e1137b0f6ad0eb923baca55
Module path: sys/modules/if_vxlan
```

## Install the Built Module

Copy the extracted module to pfSense:

```sh
scp if_vxlan.ko root@10.0.0.1:/boot/modules/if_vxlan.ko
```

Enable loading at boot on pfSense:

```sh
grep -qxF 'if_vxlan_load="YES"' /boot/loader.conf || echo 'if_vxlan_load="YES"' >> /boot/loader.conf
```

Reboot pfSense and verify:

```sh
kldstat | grep if_vxlan
```

Then apply the System Patches patch from `README.md` for the pfSense PHP/UI changes.

## Manual FreeBSD Build

If you do not want to use GitHub Actions, build on a FreeBSD 15 amd64 host that matches the target pfSense kernel generation as closely as possible.

```sh
git clone --branch releng/15.0 https://git.FreeBSD.org/src.git /usr/src
cd /usr/src
git checkout bf06074106cf80564e1137b0f6ad0eb923baca55
cd sys/modules/if_vxlan
make
```

The resulting module is normally written under `/usr/obj`, for example:

```text
/usr/obj/usr/src/amd64.amd64/sys/modules/if_vxlan/if_vxlan.ko
```

The exact path may vary depending on the build host and object directory configuration.
