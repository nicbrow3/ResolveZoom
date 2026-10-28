# ResolveZoom

A lightweight macOS menu bar app that converts trackpad pinch gestures into timeline zoom in DaVinci Resolve.

DaVinci Resolve doesn't support pinch-to-zoom on the timeline out of the box. ResolveZoom runs silently in the background and intercepts pinch gestures, converting them into the standard Alt+Scroll zoom that Resolve understands — giving you fast, intuitive timeline navigation without touching the keyboard.

---

## Features

- **Pinch to zoom** — use your trackpad naturally to zoom the Resolve timeline
- **Adjustable sensitivity** — dial in the zoom speed that feels right for you
- **Invert zoom direction** — if the default direction feels backwards, flip it
- **Multi-monitor support** — works correctly across all display configurations, including Resolve's dual-screen mode
- **Launch at Login** — start automatically with macOS
- **Minimal footprint** — lives in the menu bar, uses no resources when Resolve isn't in focus
- **Sleeps while Resolve is closed** — no event interception and, by default, no menu bar icon until Resolve opens
- **Built-in diagnostics** — the menu shows which app is detected, how many gestures were handled, and why the last one was skipped
- **Manual Resolve selection** — point the app at your Resolve install if auto-detection doesn't find it
- **Fusion-aware pinch handling** — automatically passes pinch gestures through when Fusion is active, where Resolve provides native zoom

If ResolveZoom saves you time, consider buying me a coffee — it helps keep the project going! [![Ko-fi](https://img.shields.io/badge/Buy%20me%20a%20coffee-ko--fi-FF5E5B?logo=ko-fi&logoColor=white)](https://ko-fi.com/marcinkusnierz)

---

## Requirements

- macOS 13 Ventura or later
- DaVinci Resolve (any recent version)
- A Mac with a trackpad (built-in or Magic Trackpad)

**Tested on:** macOS 26 Tahoe with DaVinci Resolve 21.0.4. Earlier versions were tested on macOS 15 Sequoia.
---

## Installation

1. Download the latest `ResolveZoom.zip` from the [Releases](../../releases) page
2. Unzip and drag `ResolveZoom.app` to your Applications folder
3. Launch the app

**First launch:** macOS will block the app because it's not signed with an Apple Developer certificate. To open it:

- Right-click (or Control-click) `ResolveZoom.app` → **Open** → **Open**

You only need to do this once. After that, the app opens normally.

**Accessibility permission:** ResolveZoom needs Accessibility access to intercept trackpad gestures. A setup window will appear on first launch guiding you through the process.

---

## Usage

Once running, ResolveZoom appears as an icon in the menu bar while DaVinci Resolve is open. Place Resolve in focus and start pinching on your trackpad — the timeline will zoom in and out.

**While Resolve is closed, ResolveZoom sleeps.** It removes its event tap, so your trackpad and scroll input don't pass through it at all, stops its page and permission checks, and hides its menu bar icon. It wakes as soon as Resolve opens and goes back to sleep when Resolve quits (or crashes). It learns about both from macOS's app launch/quit notifications, so sleeping costs nothing. To reach the menu while Resolve is closed, open ResolveZoom again from Finder or Spotlight: the icon comes back and Preferences opens. To keep the icon visible all the time, turn off **Hide menu bar icon while Resolve is closed** in Preferences.

**Menu bar status:**

- 🟢 **DaVinci Resolve active** — gestures are being intercepted
- ⚪ **Waiting for Resolve…** — Resolve is running but not in focus
- ⚪ **Asleep — Resolve isn't running** — nothing is intercepted until Resolve opens
- 🔴 **No accessibility permission** — grant access in System Settings

**Preferences** (click the menu bar icon → Preferences…):

- **Zoom Sensitivity** — controls how fast the timeline zooms. Default is 800; increase for faster zoom, decrease for finer control
- **Invert zoom direction** — reverses the zoom direction if it feels unnatural
- **Hide menu bar icon while Resolve is closed** — on by default
- **Launch at Login** — toggle autostart with macOS

ResolveZoom checks Resolve's active page once per second while Resolve is focused. The Accessibility scan runs on a background utility queue with a 50 ms IPC timeout, so an unresponsive Resolve UI cannot interrupt pinch handling.

---

## Known Issues

### "Zoom Around Mouse Pointer" is not supported

DaVinci Resolve has an option under **View → Zoom Around Mouse Pointer** that, when enabled, makes the timeline zoom towards the cursor position instead of the playhead.

**ResolveZoom does not support this setting — the timeline will always zoom towards the playhead**, regardless of whether the option is enabled in Resolve.

**Why:** ResolveZoom works by intercepting trackpad pinch gestures and converting them into synthetic Alt+Scroll events that Resolve understands as zoom commands. Resolve's "Zoom Around Mouse Pointer" feature works correctly with native hardware scroll events, but our synthetic events take a different internal code path in Resolve that always defaults to playhead-centered zoom.

Several approaches were investigated to work around this limitation:

- Marking synthetic events as "continuous" (trackpad-style) using private CGEvent fields
- Sending gesture phase information alongside the scroll event
- Injecting synthetic magnify events (type 29) directly — Resolve ignores these entirely
- Querying the DaVinci Resolve scripting API — it does not expose timeline viewport zoom control

This remains an open problem. Contributions and ideas are welcome.

---

## License

MIT License — free to use, modify, and distribute.
