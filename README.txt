collapsefix 1.0.1 - a fix for Collapse (2008)
=============================================

Fixes for Collapse (Creoteam, 2008; Steam 289620) on modern Windows. Unofficial fan fix.
Source code, details and updates: https://github.com/wh1tem0nst3r/collapsefix

There are two parts, and you need both:

  laa_patch.exe    A one-bit "Large Address Aware" patch of collapse.exe.
                   Fixes: the crash "0xc0000005 in KERNELBASE.dll" on level loads and checkpoint reloads.

  dinput8.dll      collapsefix itself, with its settings in collapsefix.ini.
  collapsefix.ini  Fixes: the Alt+Tab leak and "Cannot initialize video", unsupported display modes and
                   refresh rates, a blurry window with display scaling, the coarse game timer, the narrow
                   field of view on wide screens, the small interface on large screens, key names that
                   follow the keyboard layout, silent save failures, the game's memory leak, a navigation
                   freeze, a mouse crash during scene loads and a crash while loading saves.

The whole campaign was played through with LAA + collapsefix.
The DLL cannot replace LAA: the game reserves its memory before any DLL can run.


INSTALL
-------
Needs the Steam version of the game (collapse.exe, 2,392,108 bytes). The patcher refuses any other file.

1. Close the game. Back up your saves: Documents\Creoteam Collapse\saveGame\
2. Unzip dinput8.dll, collapsefix.ini and laa_patch.exe into the game folder, next to collapse.exe.
   (Steam -> Collapse -> Properties -> Installed Files -> Browse)
3. Double-click laa_patch.exe in the game folder. It:
   - checks that this is the right collapse.exe and refuses any other file;
   - keeps the original as collapse.exe.bak (an existing backup is never overwritten);
   - changes one bit in the exe header. Nothing else.
   Running it a second time does nothing.
4. Start the game. collapsefix.log appears next to the exe and lists every fix as OK or SKIP.

After "Verify integrity of game files" or a Steam update, run laa_patch.exe again: Steam puts the
original exe back. dinput8.dll is not a game file, so Steam leaves it alone.


UNINSTALL
---------
- Delete dinput8.dll, collapsefix.ini and collapsefix.log from the game folder.
- Steam -> Collapse -> Properties -> Installed Files -> "Verify integrity of game files" puts the
  original exe back (or run "laa_patch.exe --restore" from a command prompt in the game folder).
- Then delete laa_patch.exe and collapse.exe.bak.


SETTINGS (collapsefix.ini)
--------------------------
1 = on, 0 = off. The default is in brackets.

borderless [1]
    "Fullscreen" becomes a borderless window over the whole monitor. Alt+Tab is instant and never loses
    the graphics device. 0 = the game's own exclusive fullscreen (also fine now: Alt+Tab no longer leaks).
dpi_aware [1]
    Tells Windows the game handles display scaling, so at 125 % or 150 % the window is sharp.
alt_tab [1]
    Exclusive fullscreen only: no background re-init loop while the game is not in front.
display_mode [1]
    Exclusive fullscreen only: a Resolution your monitor does not support falls back to the desktop
    mode instead of "Cannot initialize video" and an endless retry.
refresh [0]
    Exclusive fullscreen refresh rate. 0 = same as the desktop, -1 = the game's own choice, or Hz.
vsync [0]
    V-Sync. The game's fullscreen never turns it on by itself.
timer [1]
    The game measures time in 15.6 ms steps (menus and camera judder). This gives it 1 ms steps.
save_log [1]
    Writes a line to collapsefix.log when the game fails to create a save file (it does not tell you).
fov [auto]
    Field of view. auto = Hor+: wide screens see more at the sides (90 degrees at 16:9).
    0 = the game's own. A number = horizontal degrees, 40..150. Engine cutscenes widen too.
ui_scale [auto]
    Size of the whole interface: frames, icons, crosshair and text. auto = fits the resolution
    (125 % at 1080p, 167 % at 1440p, 250 % at 4K). 100 = the game's own, or a percent, 100..400.
    The main menu keeps its size: it already follows the screen.
english_keys [1]
    Key names in hints stay English ("Press E") whatever the keyboard layout. Controls do not change.
pad_camera_deadzone [0]
    Gamepad right stick dead zone in percent, 0..90. 0 = the game's own 13 %. Try 30 if the camera
    drifts by itself on a worn stick.
input [1]
    Logs mouse and keyboard errors to collapsefix.log. Changes nothing in the game.
nav_guard [1]
    Stops an endless loop in the game's navigation code that freezes the game with the mouse moving.
release_batches [1]
    Frees the level geometry the game forgets on every load and reload. Without it memory grows until
    a crash after a few hours.
mouse_keeper [1]
    Prevents a rare crash inside Windows' dinput8.dll during a scene load.
heap_guard [1]
    Prevents a rare crash while loading a save (a bug in the game's memory allocator).

[log] enabled=0 turns the log off. [log] d3d_counts=1 is for testing only.


TROUBLESHOOTING
---------------
"Load" is grey, no saves after death, Documents\Creoteam Collapse is missing or empty
    Windows Security -> Ransomware protection -> Controlled folder access blocks the game.
    Allow collapse.exe under "Allow an app through Controlled folder access".
    collapsefix detects this at start and shows a message.

Hero under the floor or falling through the level after loading a checkpoint, then a crash
    A save with a bad game state (reported in Mark's Castle, checkpoints 3-4). Back it up, then delete
    the newest NN_level_K.jpg in Documents\Creoteam Collapse\saveGame\profile.<name>\

"STOP: hardware acceleration not available" on RTX 40 / 50 series
    Not solved yet. Players report that DXVK gets around it: put the 32-bit d3d9.dll (the x32 folder
    of a release from https://github.com/doitsujin/dxvk) next to collapse.exe. collapsefix works on top
    of DXVK (tested with DXVK 3.1.1 on an RTX 3050 Ti; not yet on an RTX 40 / 50).
    If it fails for you, please post your collapsefix.log and collapse.log.

"out of virtual memory: alloc size ..."
    The Windows page file is too small or off. Set it to "System managed size".

"d3dx9_31.dll not found"
    Install the DirectX End-User Runtime (June 2010).

Monitor says "out of range", sound plays
    Exclusive mode at a refresh rate the screen cannot show. Use refresh=0 (default) or borderless=1.

QTE arrows appear, but nothing happens
    The arrows on screen mean W/A/S/D. Press W/A/S/D, not the arrow keys.

The FOV: line disappears from setup.ini
    The game rewrites the file without it. Use fov in collapsefix.ini instead.

Stutter on a laptop on battery
    The laptop lowered the refresh rate and GPU power. Play plugged in.

The game does not start with overlays
    Steam overlay, RTSS / MSI Afterburner and compatibility mode load DLLs where the game wants its own
    memory. Turn them off for this game. Do not use compatibility mode.

setup.ini, collapse.log and the saves are in Documents\Creoteam Collapse\


KNOWN ISSUES
------------
- RTX 40 / 50 start failure: see TROUBLESHOOTING. A test on an RTX 4060 is planned.
- Two bodies stay solid black after the cutscene with Helen in the first level. A bug in the game,
  also without collapsefix. They disappear after the next checkpoint load. The cause is not known yet.
- Mouse not working (cursor stays, camera does not turn): not seen here. Please send collapsefix.log.
- Text is small or soft in places (the game's font images are small). New fonts are planned.
- No subtitles during phone calls; in some late calls the two names in the call window are swapped.
  Planned with a text patch.
- Gamepad: no button remapping.
- English text with Russianisms and empty notes: planned as a separate text patch.
- In Maydan the last save in the Load menu (14) is not the latest progress. This is how the game numbers
  those checkpoints. "Continue game" always loads the latest save.


TESTED ON
---------
Windows 11 Pro, Steam build 222225, Russian and English versions. Laptop with an AMD Radeon iGPU and an
NVIDIA RTX 3050 Ti, 1920x1080 at 144 Hz, display scaling 125 %.


CREDITS
-------
- Collapse by Creoteam (Kyiv), 2008. This is an unofficial fan fix.
- Testing and play sessions: wh1tem0nst3r. Analysis and code written with an AI agent (Claude Code,
  Claude Opus 5.5), directed and checked in play by wh1tem0nst3r.
- Prior community work: the WSGF FOV guide, the PCGamingWiki page, and the Steam Community players whose
  reports shaped the list of problems.
- Built with zig (https://ziglang.org). No third-party code is included.


LICENSE
-------
MIT (see LICENSE). It covers collapsefix and the patcher, not the game: you need your own copy of Collapse.
No game files are included.
