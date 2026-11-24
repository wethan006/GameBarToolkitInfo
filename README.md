# GameBar Toolkit

Controller tools for Windows 11: a Game Bar widget plus a small tray companion.

<!-- Add a Store badge here once the listing is live -->

## Features

- **Controller battery**: see your Xbox controller's battery in the tray and in Game Bar, with low-battery alerts.
- **Gamepad cursor**: move the mouse, click, right-click and scroll with your controller. Off until you turn it on.
- **Unpause combo**: hold up to three buttons together (default View + LB + RB) to unpause the cursor, so it can't happen by accident in a game.
- **Smart pause**: the cursor steps aside in apps and games that already support a controller, in Windows panels like Settings and Task View, and while a Windows 11 right-click menu is open. Include or ignore any app from the widget.
- **Pointer hiding**: while paused, the mouse pointer is hidden and comes back as soon as you use the mouse. The pointer is never moved.
- **Touch keyboard**: open the Windows touch keyboard with your controller and see a preview of what you're typing above it.
- **Xbox mode at start-up** (optional): open the Xbox full screen experience when you sign in.

## Requirements

- Windows 11 (Windows 10 22H2 may work but isn't the main target)
- An Xbox-compatible controller for most features

## Install

Get it from the Microsoft Store (link coming soon).

## Using it

1. Press **Win + G** and open **GameBar Toolkit** from the Widget menu. This also starts the tray companion (gamepad icon in the notification area).
2. Turn on the gamepad cursor in the widget.
3. Left stick moves the pointer, A clicks, B right-clicks.
4. Right-click the tray icon for the menu, including **Exit**.

Start with Windows is off until you turn it on in the widget.

## Limitations

- The gamepad cursor can't control apps running as administrator or the UAC prompt (Windows blocks this).
- **Not designed for competitive online games with anti-cheat.** (Untested, I don't know it anti cheat has issue. My cursor emulation runs the same as steam so I think its okay)
- In Xbox mode, LT + RT can't be used together in a combo (Windows reports both triggers on one axis).

## Privacy

Everything stays on your PC. The app doesn't use the internet, collect data, or read other programs' memory. See [PRIVACY.md](PRIVACY.md).

## Feedback and support

Found a bug or have an idea? [Open an issue](../../issues).

## License

© 2026 wethan. All rights reserved.
