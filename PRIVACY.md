# GameBar Toolkit privacy policy

This describes what the current GameBar Toolkit app (companion + Game Bar widget) does with information on your PC.
It is written from the code as it stands. Nothing is sent over the internet.

**Publisher / contact:** wethan — https://github.com/wethan006/GameBarToolkitInfo/issues

If this text is posted for the Microsoft Store, replace the line above with your real name and a way to reach you, and keep this page up to date if the app’s behavior changes.

## What the app does

GameBar Toolkit is a Game Bar widget and a companion program that runs in the tray. It can show a controller’s battery level, move a mouse pointer with a gamepad (the “gamepad cursor”), hide the system pointer while that cursor is paused, open the Windows touch keyboard from the controller and show a typing preview above it, and optionally start Xbox mode when you sign in.

It does not have an account, does not sign you in, and does not include ads or analytics.

## What is stored on this PC

Settings, a short log, and a small icon file are stored under a GameBarToolkit folder in your local app data.

When the companion is installed as part of the Microsoft Store / MSIX package, Windows keeps that folder inside the package’s data (for example under `%LOCALAPPDATA%\Packages\<package>\LocalCache\Local\GameBarToolkit`). Uninstalling the package removes it.

When you run an unpackaged build (for example from Visual Studio), the folder is `%LOCALAPPDATA%\GameBarToolkit`. An unpackaged build may also write a “Start with Windows” entry and, for notifications, a display name and icon path under your per-user registry. The packaged app does not write those registry entries; it uses the package’s own startup task and notifications.

The files are:

- **settings.json** — your preferences only: tray icon style, how often to check the battery and at what percentage to notify you, whether Xbox mode should start at sign-in, whether the tray icon is hidden, whether the gamepad cursor and typing preview are on, cursor speed, any keybinds you changed, the unpause combo, and the lists of apps you chose to always pause or never pause the cursor in. There is no name, email, or account.
- **log.txt** (and **log.old.txt** when the log is rotated at 512 KB) — time-stamped diagnostic lines: the companion starting or exiting, setting changes, errors, and similar. It may name the program in front (executable and id, not its window title, except the name of Windows’ own Task View) when the cursor auto-pauses or when a program is checked for controller support. It may note that a right-click menu was seen on the desktop or taskbar (with the menu’s class name), and it may describe a text *field* (control type, class name, automation id, framework) when typing preview is on. It does not record the text you type, the label of the field (for example “Email or phone”), or the contents of a password field.
- **gamepad.png** — a gamepad picture drawn on this PC and used on battery notifications. Notifications that use it point at this local file; they do not load anything from the network.

Settings are written on this PC only. They are not synced or backed up by the app.

## Typing preview

If typing preview is on (it is on unless you turn it off in Settings) **and the gamepad cursor is on and not paused**, a small box can appear just above the Windows touch keyboard so you can see what you are typing when the keyboard covers the field. The touch keyboard is opened from the controller, so with the gamepad cursor off, or paused (it can't be used then), the preview is off too: nothing is watched and nothing runs.

- While the keyboard is hidden the app does not poll. It waits until it opens or closes the keyboard itself, or until Windows reports a window opening in the keyboard’s own process (a window-event listener limited to that one process, not to every program). Then it looks for the keyboard for a few seconds.
- It only shows the box while that keyboard is visible.
- It first tries to read the focused field through Windows UI Automation (the text and caret). If that text does not update, it uses a low-level keyboard hook to rebuild the line from the keys you press. That hook is installed only while the keyboard is up, and only while typing preview is on.
- Password fields are never shown, stored, or written to the log. When a password field is focused, the hook does not keep the keys either. The box shows that it is a password field and hides the text.
- The text in the preview lives only on screen and in memory. It is forgotten when the keyboard is hidden or the feature is turned off. It is never written to the log or sent anywhere.

You can turn typing preview off in the widget’s Settings. While it is off, or while the gamepad cursor is off, those threads and hooks are not running.

## Other input the app uses

These stay on the PC and are used only to run the feature. All of this applies only while the gamepad cursor is on, unless it says otherwise.

- **Controller.** The gamepad cursor reads the controller (XInput, and a HID “Raw Input” path in the background when needed so it still sees buttons while another program is in front). It injects mouse and keyboard input (the Windows `SendInput` call) so the stick can move the pointer and the buttons can click or type. It never creates a virtual device, never installs a driver, and never blocks the controller from other programs.
- **Pointer hiding.** While the cursor is paused for a controller program or a full-screen program, the system pointer can be hidden with the Windows Magnification API (it is never moved or changed). The first time that is needed, the app starts that Windows component; before then, nothing about it is running. While the pointer is hidden, a low-level mouse hook watches for real mouse movement, clicks and the wheel so the pointer comes back at once (input injected by software is ignored). The hook reads each event only to decide that; nothing is kept. The pointer also comes back for an administrator window, a UAC prompt, lock or sleep, exit, or the widget’s “Show pointer / reset” button.
- **Which program is in front.** Windows tells the app when the front window changes (this part works even with the cursor off, for the widget’s “last app” and Ignore/Include buttons). For each window it opens that program’s process once, with only the limited “query” right Task Manager uses, to read its file path, package name and start time; it also checks whether it runs as administrator before hiding the pointer. It reads the window’s class and title only to recognise Windows’ own panels (Start, Settings, Task View). It never reads another program’s memory or lists its loaded libraries.
- **Right-click menus on the desktop and taskbar.** Only while a window owned by Windows’ own shell (explorer.exe: the desktop, the taskbar, File Explorer and the menus they open) is in front, and the cursor is not paused, the cursor pauses while a Windows 11 right-click menu is open, so Windows’ own controller navigation works (the older “Show more options” menu can’t be moved through with a controller, so the cursor stays active for it). It does this with Windows UI Automation (read only): it asks which element has keyboard focus and reads that element’s control type and class name, nothing else, and with two Windows window events (menu opened or closed, keyboard focus moved) that are listened to only in that situation. It never does this in games or other programs, it stops while the cursor is paused (except to notice that a menu has closed), and the UI Automation component is only started the first time it is needed.
- **Controller support check.** Once per program, only while the gamepad cursor is on, the app can check whether that program supports a controller. It does this only by reading files on this PC, never by reading another program’s memory:
  - the program’s own executable file (and a few DLL files in its folder and nearby subfolders that it uses), to see whether it is built to use a controller library (its import list, its .NET references, or the name of a controller library appearing in the file);
  - for Xbox-store games, the install folder’s package files (`MicrosoftGame.config`, `AppxManifest.xml`), if Windows lets the app read them;
  - Steam’s own local files, if Steam is installed: the Steam folder location (from your per-user registry, read only), its list of library folders, the install records of your games, and Steam’s local app-info cache, to see whether the Store lists the game as having controller support.
  These files are only read, never changed, and nothing from them is stored except the result in memory. The result (for example “imports xinput1_4.dll”, “Steam: full controller support”, or “No controller support found for Example.exe”) can be noted in the log as described above. The Steam client itself and a short built-in list (for example Battle.net and the Minecraft Launcher) are handled by name instead. You can also tell the app to always pause (Include) or never pause (Ignore) in a program; that choice is saved in `settings.json`.
- **Touch keyboard.** When you press the keyboard button on the controller, or a keybind button in the widget needs a key, the app asks Windows to show or hide the touch keyboard (or starts the On-Screen Keyboard if that is not possible). It reads, but never changes, the touch keyboard’s remembered layout setting in your per-user registry, only to write which layout it is into the log.
- **Appearance.** The typing preview and the tray icon read your per-user Windows light/dark setting (read only) to match it.
- **Controllers and battery.** Battery readings come from Windows’ device list on this PC. The app also watches Windows’ list of USB Xbox controllers to notice a wired one being plugged in or out, so the tray icon can show it.
- **Xbox mode at sign-in (off unless you turn it on).** It opens the Xbox app and presses Windows’ own Win+F11 shortcut so Windows enters Xbox mode, and looks at window titles on screen for the Xbox app’s window while doing so.

## Network

The app does not use the network. It has no internet capability, does not send telemetry, and does not open a website. The only “file” path used in a notification is the local gamepad icon mentioned above.

## What you can control

- Turn the gamepad cursor, typing preview, notifications (in Windows Settings), and “Start with Windows” on or off with the existing switches.
- Delete the GameBarToolkit data folder if you are using an unpackaged build. For the packaged app, uninstalling the package removes that data.

## Changes

If a future version of GameBar Toolkit starts collecting, storing, or sending information in a new way, this policy will be updated to match.

