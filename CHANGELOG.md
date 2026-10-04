# Changelog

All notable changes to Battery Manager are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-10-04

### Added

- While the Mac is plugged in, the menu bar battery's fill turns to flowing liquid in the animation
  color: two neighboring shades drifting left to right, a leaning sheen every few seconds with a
  band of dot-matrix sparkle in it (after Claude's "Ultracode" effort slider), and, while charging, a
  bright charge front that swells as the sheen reaches it. The digits and bolt take a deep shade of
  the color. Held at the limit (the plug) it runs slower and softer, Low Power Mode's yellow fill
  stays yellow (the color moves to the digits and the charge front), and the animation stops on
  battery. Core Animation runs it out of process (at 15–30 fps at the natural pace or slower, up to
  60 at faster speeds), so the app does no work per frame; with Reduce Motion the color shows
  without moving.
- Settings › General › Menu Bar can turn the animation off ("Animate while plugged in") and set its
  color, with a live preview of the battery beside the choices: Automatic (a shimmer on the native
  white or black fill), Ultracode (lavender drifting to periwinkle and pink), Blue, Green, Orange,
  Pink or a custom color, picked in a popover on its swatch (saturation and brightness, hue, hex).
  **Animation speed** runs it from a quarter to three times its natural pace; a change takes effect
  smoothly, from wherever the motion is.
- **Quit Battery Manager** at the bottom of the battery menu.

### Fixed

- Settings › General names **System Settings › Menu Bar** (and its **Open Menu Bar Settings…**
  button) for hiding Apple's battery icon on macOS 26 and later, where Control Center's pane was
  renamed; earlier versions still get Control Center.

## 1.0.0 - 2026-10-03

First release as a downloadable app. Earlier versions (0.x) were only built from source.

### Added

- Download and install like any Mac app: a DMG (and a zip) with a universal Apple Silicon + Intel
  build. **Install Helper** sets up the root helper from the app with an administrator prompt,
  **Update Helper** replaces one from a different version (the helper now reports its version), and
  **Uninstall Battery Manager…** removes it and restores macOS's charge limit.
- macOS 27 support: a backend that drives macOS's own Charge Limit (System Settings › Battery)
  through PowerUI, enforced by macOS through sleep, restarts and shutdown. It checks every selector
  before use, saves and restores the original limit, adopts edits made in System Settings and
  doesn't fight macOS's own gauge calibration.
- **When above the limit**: **Discharge to the limit** (System Settings always shows your limit) or
  **Hold the current charge**; also the menu's **Discharge to Limit** toggle and
  `batteryd discharge on|off`.
- Adapter control (`CHIE`) detected separately from the charge keys, so discharge works on
  macOS 27, and an opt-in for limits below 80%.
- An Apple-style battery menu that replaces Apple's battery icon: the same glyph (bolt, plug, Low
  Power and low-battery colors), Battery, Power Source, Energy Mode with a Low Power toggle and
  Using Significant Energy, on Liquid Glass with macOS 27's status item session, and the charge
  limiter below.
- Calibration, heat protection, MagSafe light modes, power flow and a daily health chart,
  notifications, a Settings window, and `batterymanager://` links (including `topup/cancel`).
- CI on Linux and macOS, and a license (CC BY-NC-SA 4.0).

### Security

- The root helper is installed in `/Library/PrivilegedHelperTools`, which only root can change.
  `/usr/local/bin` (where 0.x put it) is often owned by the user, for example with Homebrew, which
  let any process running as the user replace the binary root runs; it's now only a symlink for
  the command line and is never executed by the installer. The installer refuses directories
  others can write to and never follows a link for the settings file.
- `batterymanager://` links that let the battery charge further (turning the limit off, raising
  it, top-up, calibration) ask first, since any app or web page can open them.

### Changed

- The app is lighter in the background: it republishes only real changes (no redraw of the icon
  and menu every 5 s), refreshes every 30 s with tolerance when nothing is open plus on every
  battery change macOS announces, releases the Settings window when it's closed, and opening the
  menu no longer waits on `pmset`.
- Tests use Swift Testing, so `swift test` works with only the Command Line Tools.
- `scripts/install.sh` builds the same app bundle as the DMG and runs its bundled helper installer.

### Fixed

- Low Power Mode staying on after a quick on/off toggle while the menu showed it off: requests are
  told apart to the millisecond (older whole-second files still load) and the menu re-reads the
  real state after each toggle.
- Right-clicking the icon on macOS 27 also opened the battery menu on top of the options menu, a
  stale right-click could reopen the options menu, and VoiceOver's Control-Option-Space opened it.
- Charge policy: the adapter is never cut while unplugged, heat protection no longer stops a
  discharge to the limit, and a hold never turns macOS's limit off (at most 99%).
- System Settings edits, **Charge to Full Now** and kept top-ups are handled the same way by the
  helper and the app, including across restarts.
- The battery menu reports against macOS's Charge Limit (`Charging to 80% Limit`) as Apple's does.
- The "helper stopped" notification fired after every wake or not at all; top-up and calibration
  notifications fired for runs that didn't finish.
- An empty `config.json` during a save no longer resets settings, and a status file from a newer
  helper no longer reads as no helper at all.
- Sleep: the adapter is always reconnected first, so the battery is never left draining
  unsupervised; in hold mode the limit is put back at yours before sleeping on battery, so plugging
  in while asleep can't charge past it; Intel calibration can't be refilled by sleep; a charge
  interrupted by sleep resumes after wake on SMC Macs; draining from the 80% safety net no longer
  keeps the Mac awake; and dark wakes no longer trigger a false "helper stopped" notice.
- Updating from a 0.x helper no longer repeats a finished top-up or calibration.

[1.1.0]: https://github.com/somealtmail-sudo/Battery-Manager-for-Mac/releases/tag/v1.1.0
