# BugSack (Vanilla 1.12.1 / Turtle WoW)

Error viewer, chat/sound notifier, and FuBar plugin for **!BugGrabber** in World of Warcraft 1.12.1 (strict Lua 5.0), upgraded from upstream **BugSack v2.1.0**.

## Client DLL Requirements

- **Required DLLs:** None. Runs purely on stock WoW 1.12.1 / Turtle WoW Lua 5.0 with zero external DLL dependencies.
- **Required Addon:** `!BugGrabber` (`## RequiredDeps: !BugGrabber`).

## Features

- **Error Viewer Frame:** Browse errors by `current`, `session`, `previous`, session number, or `all`, with First / Prev / Next / Last navigation, syntax-highlighted stack traces, and copyable text.
- **Movable & Escape-Friendly UI:** Drag `BugSackFrame` anywhere on screen and close it anytime with `Escape` (`UISpecialFrames`) without stealing keyboard movement focus.
- **Notifications:** Configurable auto-popup (`auto`), chat summary/full-message output (`chat` / `msg`), and audible alert (`mute`).
- **!BugGrabber Settings Integration:** Toggle Persistent SavedVariables (`save`), set history limit (`limit`, 10–100), generate test errors (`bug script` / `bug addon`), and wipe errors (`reset`).
- **FuBar Support (`BugSackFu`):** Shows unique/duplicate error counts (`unique/total`), quick-click session viewer, and clickable recent-error tooltip.
- **Slash Commands:** `/bugsack` or `/bs`.

## What Was Fixed & Improved vs Upstream (v2.1.0)

- **Fixed `OnError` Bucket-Table Mismatch:** Fixed `BugSack:OnError(err)` passing the AceEvent bucket table directly into `FormatError()` when `chat` + `msg` are enabled (which previously printed `"Tried to format an error of type \"table\", should be a table."`), and wrapped startup `bugsackErrors` into a proper bucket table (fixing `"4 errors have been recorded."` on a single startup error).
- **Closed `ADDON_LOADED` -> `PLAYER_LOGIN` Startup Gap:** Moved `BugGrabber.bugsackErrors` handoff from `OnInitialize()` to `OnEnable()` (right after registering `BugGrabber_BugGrabbed`) so load-time errors from addons alphabetically after `BugSack` are captured by `BugSack` instead of leaking to chat.
- **Fixed Session-Number Filtering:** Coerced string arguments in `ShowByNumber()`, `ListByNumber()`, and `GetErrors()` with `tonumber()` so `/bugsack show number <n>` and `/bugsack list number <n>` match numeric `err.session` values, and guarded `UpdateFrameText()` with `tonumber(self.which) or 0`.
- **Fixed Keyboard Focus Steal & `Escape` Handling:** Changed `BugSackFrameScrollText` `autoFocus` from `"true"` to `"false"` in `BugSack.xml`, cleared editbox focus on `OnHide`, and registered `BugSackFrame` in `UISpecialFrames`.
- **Made `BugSackFrame` Draggable:** Added `SetMovable(true)`, `EnableMouse(true)`, and `LeftButton` drag handlers in `OnInitialize()`.
- **Refreshed Open Frame on `Reset()`:** Automatically updates `BugSackFrame` if it is open when errors are cleared, and guarded `BugGrabberDB` access.
- **Hardened `FormatError()` & Truncation:** Added `nil`-safe fallbacks for `err.session`, `err.counter`, and `err.message`, and appended `"|r"` before `(... more ...)` when truncating messages over 4,000 characters.
- **Fixed `BugSackFu` Tooltip Crash:** Replaced `string_gmatch(...)()` in `BugSackFu:OnTooltipUpdate()` with `string.find(..., ": ([^\n]+)")` and safe fallbacks so single-line errors without a trailing newline never crash `string.format`.

## Credits

- **Original Authors:** Fritti, Rabbit
- **X-Credits:** Rowne, Ramble, industrial, kergoth, ckknight, Gamefaq, thomasmo, damjau
- **Testers:** Ramble, Sariash
