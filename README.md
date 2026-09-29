
# Jailbreaking iPhone 6s on iOS 10.3.1 and Installing MikuFlick 02

> Tested environment: iPhone 6s / A9 / iOS 10.3.1  
> Date tested: 2026  
> Goal: jailbreak iOS 10, install AppSync, restore USB filesystem access, and install/run MikuFlick / MikuFlick 02.
>
> This document records a setup that was actually tested and confirmed working. The iOS 10 jailbreak ecosystem is very old, and different devices, bootstraps, and repositories may behave differently. Do not blindly follow instructions written for other iOS versions.

---

## 0. Final Working Environment

The following setup was confirmed working:

- iPhone 6s
- iOS 10.3.1
- Apple A9
- TotallyNotSpyware / TNS web jailbreak
- Zebra
- AppSync Unified 68.0
- Substrate Safe Mode
- Cydia Substrate 0.9.7020
- Saurik Apple File Conduit "2" 1.2
- MikuFlick 02 IPA

The final setup supports:

- Installing old IPA files without re-signing
- Running MikuFlick 02
- Accessing the jailbroken filesystem over USB
- Replacing `Localizable.strings`
- Importing save data and modifying game resources

---

# 1. Erase the Device

The device was first erased and configured again.

Go to:

```text
Settings
→ General
→ Reset
→ Erase All Content and Settings
```

Important:

**Do not perform a normal full restore using Finder or iTunes.**

iOS 10.3.1 has not been signed by Apple for many years. A normal Apple restore may upgrade the device to a newer signed version of iOS.

In this case, only user data was erased. The IPSW itself was not reflashed.

If the device has Activation Lock enabled, activate it normally using the Apple ID originally associated with the device.

---

# 2. Complete Initial Setup

Complete the iOS 10 initial setup and connect the device to Wi-Fi.

In this case, i4Tools was used to assist with skipping some parts of the initial setup process.

Check:

```text
Settings → General → About
```

Make sure the system is still:

```text
iOS 10.3.1
```

---

# 3. Fix TLS / Root Certificates

The modern Internet has moved far beyond the root certificate store included with iOS 10.

Without updated certificates, you may encounter:

- Safari being unable to open HTTPS websites
- Zebra failing to refresh repositories
- TLS / SSL errors when accessing repositories
- The TNS webpage failing to load correctly

Use:

```text
http://tlsroot.litten.ca
```

Install:

```text
Signed iOS Bundle (iOS 5+)
```

---

## Profile Date / Time Problems

Older versions of iOS may reject signed configuration profiles because of certificate validity dates.

If this happens:

1. Disable automatic date and time
2. Temporarily set the date to an older year
3. Install the certificate profile
4. Immediately restore the correct current date
5. Re-enable automatic date and time

Important:

**Modern HTTPS websites require the correct current date.**

Otherwise, certificate validation may fail because of `Not Before` / `Not After` checks.

---

# 4. Jailbreak Using TNS

The iPhone 6s running iOS 10.3.1 can use the currently maintained TotallyNotSpyware / TNS jailbreak.

Open:

```text
https://lukezgd.github.io/tns
```

Then:

```text
Slide for Spyware
→ noot noot
```

After a successful jailbreak, Zebra will be installed.

TNS is a semi-untethered jailbreak.

This means:

```text
Every full reboot
→ jailbreak state is lost
→ open the TNS webpage again
→ re-enable the jailbreak
```

Installed jailbreak packages remain installed after a normal reboot.

---

# 5. Zebra Network Problem on Chinese iOS 10 Devices

This was one of the most notable problems encountered during testing.

Symptoms:

```text
Zebra opens normally,
but every repository reports:

"It appears you are not connected to the Internet."
```

This may happen even when:

- Wi-Fi works
- Safari can access the Internet
- The device is connected to the international Internet
- HTTPS websites work correctly

This appears to be related to an old App network permission mechanism used by Chinese-region versions of iOS.

You may find that:

```text
Settings
→ Wi-Fi
→ Apps Using WLAN & Cellular
```

does not contain Zebra at all.

---

## What Worked in This Case

After erasing the device and running TNS again, Zebra successfully obtained network access during its first initialization.

A successful repository refresh looked similar to:

```text
https://getzbra.com/repo/       Done
https://repo.chariz.com/        Done
https://havoc.app/              Done
http://apt.thebigboss.org/...   Done
```

If only a few old repositories fail, such as:

```text
apt.saurik.com
```

while other HTTP and HTTPS repositories work, Zebra's network access is functioning.

---

# 6. Do Not Rely on Modern AppSync Builds

Modern versions of AppSync Unified use dependency metadata that can be awkward with this old iOS 10 jailbreak environment.

The version used successfully in this setup was:

```text
AppSync Unified 68.0
```

Old package ID:

```text
net.angelxwind.appsyncunified
```

AppSync Unified 68.0 is from the 2019 era and works well with older iOS 10 jailbreak environments.

---

## AppSync Unified 68.0 SHA256

After downloading the old `.deb`, verify its SHA256 hash:

```text
f8ccdd339173dff03fd923e32b9b67e195c8c1490c5dd914e503e90488c569da
```

On macOS:

```bash
shasum -a 256 net.angelxwind.appsyncunified_68.0.deb
```

Only install it if the hash matches.

---

# 7. iOS 10 Does Not Have a Proper Files App

One of the biggest usability problems with iOS 10 is:

```text
There is no modern Files app.
```

Managing downloaded `.deb` files in Safari is therefore inconvenient.

In this setup, a Mac was used as a temporary HTTP file server.

Open Terminal in the directory containing the `.deb` file:

```bash
python3 -m http.server 8080
```

Find the Mac's local IP address, for example:

```text
192.168.1.100
```

Then open Safari on the iPhone and visit:

```text
http://192.168.1.100:8080/
```

Tap:

```text
net.angelxwind.appsyncunified_68.0.deb
```

Then choose:

```text
Open in Zebra
```

Install the package locally using Zebra.

---

# 8. Install Cydia Substrate

This device uses:

```text
iPhone 6s
A9
iOS 10.3.1
TNS
```

The final working configuration uses the traditional Cydia Substrate environment.

Version used:

```text
Cydia Substrate 0.9.7020
```

Package filename:

```text
mobilesubstrate_0.9.7020_iphoneos-arm.deb
```

If Zebra reports that the following dependency is missing:

```text
com.saurik.substrate.safemode
```

install:

```text
Substrate Safe Mode
```

The version used here was:

```text
Substrate Safe Mode 0.9.6001
```

Then install:

```text
Cydia Substrate 0.9.7020
```

---

# 9. Install AFC2

The goal is to access the complete jailbroken filesystem from a computer over USB.

There is an important trap here.

## Do Not Install the Old `afc2add`

Wrong package:

```text
afc2add
us.scw.afctwoadd
```

Installing it may produce errors such as:

```text
/System/Library/Lockdown/Services.plist
File not found
```

This is a very old AFC2 implementation and is not appropriate for this iOS 10 setup.

---

## Use Saurik Apple File Conduit "2"

The working package used here was:

```text
Apple File Conduit "2"
Version 1.2
```

Package ID:

```text
com.saurik.afc2d
```

It requires:

```text
Cydia Substrate
```

So the correct installation order is:

```text
Substrate Safe Mode
↓
Cydia Substrate 0.9.7020
↓
Apple File Conduit "2" 1.2
```

After installation:

```text
Reboot the iPhone
→ run TNS again
→ restore jailbreak state
→ disconnect and reconnect USB
```

After that, tools such as i4Tools / iFunBox should be able to access the jailbroken filesystem.

---

# 10. Install MikuFlick 02

Once AppSync is working, IPA installation can begin.

In this case:

```text
MikuFlick 02 installed successfully
```

This confirms that:

```text
AppSync
+
Substrate
+
installd
```

are working correctly.

---

# 11. Do Not Download Large IPA Files Through Safari

This was another major problem encountered during testing.

iOS 10 has extremely poor file management.

When Safari downloads a large IPA, installation may temporarily require space for:

```text
IPA archive
+
installation staging files
+
extracted .app
+
Safari download cache
```

On old 16 GB or 32 GB devices, this can quickly consume all free storage.

You may suddenly see:

```text
Available storage: 0 bytes
```

A reboot may not recover all of the space immediately.

---

## Recommended Method

Keep large IPA files on the Mac and install them over USB.

Recommended method:

```text
Legacy iOS Kit
→ App Management
→ Install IPA (ideviceinstaller)
```

Or use:

```bash
ideviceinstaller install MikuFlick02.ipa
```

This avoids storing the full IPA inside Safari's download area first.

---

# 12. Remember to Re-Jailbreak After Every Reboot

TNS is semi-untethered.

After:

```text
Power off
Reboot
Battery depletion
```

you must run:

```text
Safari
→ TNS
→ Slide for Spyware
→ noot noot
```

again to restore the jailbreak state.

Otherwise:

- Zebra may open but behave incorrectly
- Substrate may not work
- AppSync hooks may not be active
- AFC2 may be unavailable

---

# 13. MikuFlick 02 Localization

After installation, AFC2 can be used to modify the app bundle directly.

Find:

```text
MikuFlick02.app
```

Then:

```text
en.lproj/Localizable.strings
```

For the MikuFlick 02 localization file in this project:

```text
Localizable2.strings
```

rename it to:

```text
Localizable.strings
```

before copying it into the game.

Replace:

```text
MikuFlick02.app/en.lproj/Localizable.strings
```

Back up the original file before replacing it.

Then fully close and relaunch the game.

---

# 14. Final Working Procedure

The complete working procedure can be summarized as:

```text
iPhone 6s / iOS 10.3.1
        ↓
Erase user data
        ↓
Complete activation / initial setup
        ↓
Install modern TLS root certificates
        ↓
TNS web jailbreak
        ↓
Zebra
        ↓
Use Python HTTP server to transfer local .deb files
        ↓
AppSync Unified 68.0
        ↓
Substrate Safe Mode
        ↓
Cydia Substrate 0.9.7020
        ↓
Apple File Conduit "2" 1.2
        ↓
Reboot
        ↓
Run TNS again
        ↓
USB / AFC2
        ↓
Install MikuFlick 02 IPA
        ↓
Replace Localizable.strings
```

---

# 15. Methods Confirmed to Be Invalid or Not Recommended

## Meridian

Meridian was tested during this process.

Because a TNS bootstrap was already present, Meridian reported:

```text
please erase
```

Do not mix another jailbreak bootstrap into an existing TNS environment.

---

## Modern AppSync 116.x

Modern AppSync versions may introduce dependency behavior involving:

```text
mobilesubstrate
```

and other newer packaging assumptions.

This setup ultimately used:

```text
AppSync Unified 68.0
```

---

## afc2add 1.01

This package attempts to modify:

```text
/System/Library/Lockdown/Services.plist
```

and failed in this setup.

Do not use it.

---

## iOS 11+ AFC2 Builds

Do not install AFC2 packages that explicitly require:

```text
firmware >= 11.0
```

This device is running:

```text
iOS 10.3.1
```

---

## Downloading Large IPA Files Through Safari

Strongly not recommended.

iOS 10 lacks the modern Files / Downloads workflow, and large IPA files can easily consume all available storage.

For large IPA files, use:

```text
Mac
→ USB
→ ideviceinstaller
```

---

# 16. Packages Worth Archiving

Because old repositories may disappear in the future, it is strongly recommended to archive the `.deb` packages that were confirmed working:

```text
AppSync Unified 68.0

Substrate Safe Mode 0.9.6001

Cydia Substrate 0.9.7020

Apple File Conduit "2" 1.2
```

Also save:

```text
SHA256
Source URL
Version
Package ID
```

This allows the setup to be reproduced later using:

```bash
python3 -m http.server 8080
```

even if the original repositories disappear.

---

# 17. Recommended Backups

For devices still running iOS 10, it is also recommended to preserve:

- IPA files
- Localization files
- Save data
- Required `.deb` packages
- TLS root certificates
- SHSH / onboard blobs
- The matching iOS 10 IPSW

The hardest part of this setup to replace is often:

```text
the old iOS version itself
```

---

# 18. Current Status

Final tested configuration:

```text
iPhone 6s
iOS 10.3.1
TNS Jailbreak
Zebra
AppSync Unified 68.0
Cydia Substrate
AFC2
MikuFlick 02
```

MikuFlick 02 has been successfully installed and launched.

Possible future work:

```text
Localization
Save import
DLC / song pack preservation research
Resource analysis
64-bit modernization
iOS 27 reimplementation research
```

---

## Disclaimer

This guide is intended for:

- Research on devices you own
- Legacy software preservation
- Compatibility testing
- Localization research
- Personal backup and use of software you legally obtained

It does not include instructions for bypassing Activation Lock or obtaining unauthorized commercial content.

Old iOS versions, jailbreaking, and filesystem modification can cause boot loops, data loss, or security issues. Back up important data before proceeding.
```
