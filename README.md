# LetsVoice Local Mobile

LetsVoice is a local-first mobile transcription, notes, and on-device AI app.
The repository targets Android phones and iPhone; it does not contain an iPad,
Mac, or App Store distribution target.

## Windows team members: Install the iPhone test build without a Mac

LetsVoice is not published on the App Store. Team members who do not have a Mac
or a paid Apple Developer Program account can install the IPA provided by the
team using **Windows + SideStore + their own free Apple Account**. A Windows
computer is needed only for the initial SideStore setup; subsequent refreshes
can usually be completed on the iPhone over Wi-Fi with LocalDevVPN.

### 1. Prepare the devices and account

- A 64-bit Windows 10 or Windows 11 computer. Windows 10 on ARM is not currently
  supported by SideStore's tools.
- An iPhone running iOS 16.4 or later with a device passcode configured.
- An iPhone data cable and Wi-Fi. SideStore's initial setup and refresh process
  cannot rely solely on a cellular network.
- Each tester's own Apple Account. Do not share accounts, passwords,
  verification codes, or device pairing files.
- Install `LocalDevVPN` from the App Store on the iPhone and allow it to add a
  VPN configuration.

SideStore's system requirements and download locations may change. Before you
begin, open the official SideStore [Prerequisites](https://docs.sidestore.io/docs/installation/prerequisites)
and [Install](https://docs.sidestore.io/docs/installation/install) pages.

### 2. Install SideStore on Windows

1. Install iTunes according to SideStore's official prerequisites. The current
   recommendation is to try the version provided on Apple's website first. If
   iTunes cannot recognize the phone, try the Apple Devices app instead.
2. Download and install the Windows version of `iloader` from the official
   SideStore page. The MSI package is recommended.
3. Connect the iPhone with a data cable, select **Trust This Computer** on the
   phone, and enter the device passcode.
4. Open `iloader`, sign in with your own Apple Account, and select your iPhone.
5. Click `Install SideStore (Stable)` and wait for the installation to finish.
6. On the iPhone, open **Settings > General > VPN & Device Management**, then
   trust the developer app associated with the Apple Account.
7. Open **Settings > Privacy & Security > Developer Mode**, enable it, and
   restart the iPhone when prompted.
8. Connect `LocalDevVPN`, open SideStore, and sign in with the same Apple Account
   used in iloader.
9. Open `My Apps` and tap `7 DAYS` next to SideStore to complete the first manual
   refresh.

### 3. Download and verify LetsVoice

Download these two files from the team's
[iOS v1.6.2 Release](https://github.com/dhebhxh/speakspace-local-mobile/releases/tag/ios-v1.6.2):

- [`LetsVoice-iOS-v1.6.2.ipa`](https://github.com/dhebhxh/speakspace-local-mobile/releases/download/ios-v1.6.2/LetsVoice-iOS-v1.6.2.ipa)
- [`LetsVoice-iOS-v1.6.2.ipa.sha256`](https://github.com/dhebhxh/speakspace-local-mobile/releases/download/ios-v1.6.2/LetsVoice-iOS-v1.6.2.ipa.sha256)

Place both files in the same folder and run the following commands in PowerShell:

```powershell
Get-FileHash .\LetsVoice-iOS-v1.6.2.ipa -Algorithm SHA256
Get-Content .\LetsVoice-iOS-v1.6.2.ipa.sha256
```

The two SHA-256 values must match exactly. The correct value for this release is:

```text
d5568e676cf9efaa2f4f38fbff88c2e3ebfd13fdfd6bd2787a9067811481eaeb
```

If a rollback is required, the previous
[iOS v1.6.1 Release](https://github.com/dhebhxh/speakspace-local-mobile/releases/tag/ios-v1.6.1)
remains available. Do not roll back by uninstalling and reinstalling the app on
the same iPhone, because doing so deletes its local data.

### 4. Install and refresh with SideStore

1. Save the IPA to the Files app on the iPhone, or open the release download
   directly on the iPhone.
2. Connect `LocalDevVPN` and select SideStore from the share menu. You can also
   use SideStore's option for adding an IPA.
3. Wait for SideStore to finish re-signing and installing the app. Do not close
   SideStore or disconnect the VPN during the process.
4. Open LetsVoice and grant microphone permission. On the `AI` screen, download
   and enable the required STT, LLM, and TTS models. Keep LetsVoice in the
   foreground while models are downloading.
5. Every five to six days, connect `LocalDevVPN`, open `My Apps` in SideStore,
   and tap the remaining-days indicator next to LetsVoice to refresh it.

A free Personal Team provisioning profile is valid for only seven days, so the
free setup cannot become a permanent one-time installation. Refreshing does not
normally clear data, but **do not uninstall LetsVoice**. Uninstalling causes iOS
to delete local notes, recordings, workspaces, chats, and downloaded models. Do
not download installation packages from third-party cloud drives, shared
enterprise certificates, or websites claiming to offer permanent signing.

For a more complete illustrated walkthrough, acceptance steps, and
troubleshooting, see the
[Windows + SideStore Chinese guide](docs/ios-sidestore-windows.md).

## Current iPhone baseline

- iPhone only, portrait orientation
- iOS 16.4 or later
- Local development or release installation through Xcode
- User audio, transcripts, notes, workspaces, and chats remain in the app's
  local container
- Network access is used only when the user starts a model download
- Live recording and imported audio are limited to two hours
- Imported audio: WAV, MP3, M4A, AAC, or FLAC, up to 2 GB
- Parakeet and multilingual Whisper STT models are supported

See [docs/ios-local-install.md](docs/ios-local-install.md) for the complete
iPhone setup and signing procedure. Record the physical-device results in
[docs/ios-device-acceptance.md](docs/ios-device-acceptance.md).

The engineering work, decisions, failures, and fixes behind the iPhone port are
documented in [docs/ios-port-development-YQ.md](docs/ios-port-development-YQ.md).
The current stable release is documented in
[docs/ios-release-v1.6.2-YQ.md](docs/ios-release-v1.6.2-YQ.md), and version-level
changes are listed in [CHANGELOG.md](CHANGELOG.md).

## Development

Install the exact locked dependencies:

```bash
npm ci
```

Create and run a local development build:

```bash
npx expo run:ios --device
```

Install a standalone local Release build that does not start Metro:

```bash
npm run ios:device:release
```

When a Personal Team needs its own unique bundle identifier, prefix the Expo
command with `IOS_BUNDLE_IDENTIFIER=com.example.letsvoice.local`. The checked-in
default remains the team identifier and contains no contributor-specific signing
information.

This app uses custom native modules and therefore cannot be tested completely
in Expo Go. For an Android development build, use `npx expo run:android` on a
machine with the Android SDK installed.

## Checks

```bash
npm test
npx tsc --noEmit
npm run lint
npx expo-doctor
```

The native projects are generated by Expo Continuous Native Generation and are
ignored by Git. Native functionality that must survive regeneration lives under
`modules/` or in Expo app configuration.

Create a SideStore-ready IPA from a verified device Release app bundle:

```bash
npm run package:ios:sidestore -- /absolute/path/to/LetsVoice.app
```

The command writes the IPA and its SHA-256 checksum under `dist/ios/`. These
release artifacts are intentionally excluded from Git and are attached to a
GitHub Release instead.

## Model downloads

Speech, language, and voice models are not bundled with the app. Install them
from the AI screens after the app is running. LetsVoice checks free storage
before each large operation and never deletes user data automatically. Keep the
app in the foreground while a model download is running.
