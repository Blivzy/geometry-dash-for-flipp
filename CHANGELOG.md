# Changelog

## 1.5 - practice mode respawn fix, manual checkpoints, boot splash
- `window_reset` no longer replays every object from index 0 on a checkpoint respawn: it now seeks the active window straight to the first object still in range. On a deep checkpoint this was a full linear scan-and-discard of the level array on every single death, which is the stutter practice mode produced past the first checkpoint; fixed load/respawn behavior verified with a host test (`game_load_level` -> forced checkpoint -> `game_respawn` -> active window actually contains objects at the new camera position).
- New `game_force_checkpoint()`: Left D-pad while playing in practice now drops a checkpoint under the player immediately, independent of touching a level `OBJ_CHECKPOINT`. Plays the checkpoint SFX and a short vibro.
- Checkpoint diamonds that have already been banked (`gx <= checkpoint.x`) now render filled with a check-tick above them instead of the plain outline; the practice HUD label also shows a small tick whenever a checkpoint is currently saved.
- New boot splash (`STATE_BOOT`): dotted-grid background, GEODASH title, a GD-style play-button glyph, "made by blivzy" with animated loading dots, and a short rising-triad boot chime (`SFX_BOOT`). Dismisses on any key press or after ~1.6 s.

## 1.4 - smoothness, speed setting, audio, readability (gameplay patterns untouched)
- Main loop paces physics from the queue timeout instead of a FuriTimer: a stall (SD write, slow frame) can no longer queue up to 32 ticks and replay them as a fast-forward burst. Catch-up is capped at 2 ticks and a long stall resyncs.
- Draw callback copies the world under the mutex (~4 KB) and draws lock-free, so a slow frame cannot block physics and physics cannot block a frame. Orientation changes and save writes moved out of the mutex.
- New Settings -> Speed: Normal (22 ms, default), Slow (28 ms), Fast (16 ms = old pace). Reuses the old unused `tilt_control` byte, so save v2 files stay valid. Physics constants are unchanged; only the real-time length of a tick changes.
- Audio: fixed a race where the thread read `tier` twice (could index tracks[-1] when music stopped); bass notes below 100 Hz were silently dropped, now all are audible; speaker retuned on note edges instead of every 8 ms; articulated notes; three new longer loops; stack 3072.
- Spikes: solid silhouette with a 1 px halo cut into the background; horizon dither bands; levels 3, 6, 8 and 9 use the inverted palette; airborne cube uses fewer lines.
- Toast ("INSANE MODE") now expires on wall-clock time; before it never cleared on the main menu.

## 1.3 - level themes, faces, cleaner rendering (gameplay untouched)
- New `theme.c/.h`: every level has its own background (squares, grid, triangles, skyline, stripes, circles), ground texture (dash, brick, zig-zag, dots) and block style (frame, cross, solid, stud).
- Difficulty faces (easy ... demon with horns) in level select.
- Redesigned ship: cockpit dome on a pointed hull that shears with its tilt.
- graphics.c reorganised into background / ground / object / player / HUD sections; removed dead code; every style is data-driven.
- tools/host_render can now capture animations (`frames2gif.py`) in addition to stills.
- Verified: physics hash identical to 1.0 on levels 1-8 and to 1.2 on all 9; zero warnings.

## 1.2 - wave mode + level 9
- New **wave** game mode: 45 degree dart, hold = climb, release = dive, slides along floor/ceiling, zig-zag trail, wave portal.
- New level 9 "Wave Rush" (cube, ship, ufo and three wave corridors). Levels 1-8 are byte-identical to 1.0 and play bit-identically.
- Save format v2 (9 levels); v1 saves migrate automatically, nothing is lost.
- HUD gets backing plates so text stays readable over solid blocks; crash screen shows NEW BEST!.
- tools/host_tests/wave.c proves every wave corridor is survivable from floor, mid-air and ceiling entries.

## 1.1 - visual overhaul (gameplay untouched)
- Spikes were drawn upside-down (wide end up); now point the right way with a hollow notch, ceiling spikes point down.
- Cube has a face and visibly tumbles in the air; ship tilts with velocity and shows a thruster flame; ball spokes spin; UFO lights blink.
- Ring-shaped portals with the target mode icon, pulsing orbs, chevron pads, spinning coins, diamond checkpoints.
- Scrolling textured ground, pit edges, drifting background squares, twinkling stars.
- Geometry Dash style framed progress bar (with a tick at your best %), "Attempt N" text at level start, death shockwave ring + screen flash, landing/run dust, ship exhaust trail.
- Redesigned main menu (cube hops when you move the cursor), level select with progress bars, settings with right-aligned values and volume meter, pause/crash/complete panels with attempts and coin icons.
- HUD adapts to the 64 px wide vertical viewport.
- Unchanged on purpose: physics, level data, audio, save format (existing saves keep working).
- New dev tools: tools/host_render (software canvas -> PNG preview), tools/host_tests/trace.c (bit-exact physics regression).

## 1.0 - initial release
- 8 levels, 4 player modes, practice mode, insane easter egg.
- Piezo chiptune sequencer on its own thread; vibration on death/complete/insane.
- Save file at /ext/apps_data/geodash/save.dat.
- Not yet verified on hardware; see README "Verification status".
