# patches_oneui8_a16port

Smali patches used in a14revive version 4.1 project

Each target JAR/APK is decoded with apktool, patched, rebuilt, and re-signed with
the AOSP platform key.

## Requirements

- Android SDK build-tools on `PATH` (for `zipalign` and `apksigner`)
- Java (for apktool)
- `apktool.jar` and the AOSP `platform.pk8` / `platform.x509.pem` (downloaded
  automatically on first run)

## Usage

Put the stock A166B files in `apks/`, then:

```sh
./run_local.sh                 # build every target
./run_local.sh SecSettings.apk # build one target
```
```sh
./make_patch.sh open   <target>            decode into unpacked/<base>_work/ and snapshot it
./make_patch.sh diff   <target> [out.patch] write the patch from your edits
./make_patch.sh verify <target> [out.patch] fresh decode + git apply --check
./make_patch.sh reset  <target>            throw away edits, back to pristine
./make_patch.sh clean  <target>            delete the work dir

Layout: apks/ inputs, patches/ patch files, unpacked/ work dirs.
```

Signed outputs land in `dist/` as `<name>_patched.<ext>`. Full apktool output
goes to `build.log`; the terminal only shows status lines.

To regenerate a patch after editing decoded smali: `./make_patch.sh diff <target>`.

## What gets patched

- **framework.jar / services.jar** – platform signature spoof, CoreRune/Freecess
  device detection, RescueParty and vendor-mismatch off, KnoxPatch hooks, deknox,
  FLAG_SECURE bypass, CSC-free call recording, AppLock support.
- **knoxsdk.jar / samsungkeystoreutils.jar / KmxService.apk** – Knox integrity
  bypass (Secure Folder, attestation).
- **ssrm.jar / SamsungDeviceHealthManagerService.apk** – DVFS/SSRM retargeted to
  s5e3830 (Exynos 850).
- **SystemUI.apk** – status-bar net speed, power-off lock, Secure Folder quick
  toggle, Smart View tile, Blue Light Filter fix.
- **SecSettings.apk / SecSettingsIntelligence.apk** – AppLock, Outdoor mode,
  deknox settings entries.
- **DAAgent.apk** – Dual Messenger for all apps.
- **Traceur / DeviceDiagnostics / ManagedProvisioning / StorageManager / KnoxCore**
  – deknox removals.

## Credits

- [UN1CA](https://github.com/salvogiangri/UN1CA) – signature, dvfs, knoxpatch,
  deknox, csc, daagent.
- [ExtremeROM](https://github.com/ExtremeXT/ExtremeROM) – FLAG_SECURE bypass, call recording, AppLock, Outdoor mode.
