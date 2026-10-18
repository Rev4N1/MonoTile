# MonoTile

**A one-tap Mono Audio toggle for your Android Quick Settings.** Requires root.

Android hides the mono audio switch deep inside *Settings → Accessibility → Audio*. That's a pain if you sometimes listen with a single earbud or share a pair with someone. MonoTile puts the switch one swipe away and keeps it in sync with the real system setting.

## Features

- **One-tap toggle** – tap the tile to switch mono audio on or off.
- **Accurate tile state** – the tile shows active or inactive based on the real system value, not a guess.
- **Long-press to resync** – long-press the tile to re-read the setting if you changed it somewhere else (for example in Accessibility settings).
- **Bluetooth aware** – the tile resyncs when a Bluetooth device connects, and mono audio is **turned off automatically when Bluetooth disconnects**, so your phone speaker goes back to stereo.

## Requirements

- A **rooted** Android phone. MonoTile writes `master_mono`, a hidden system setting that normal apps can't change.
- Android 7 or newer (needed for custom Quick Settings tiles).

## Installation

There are two ways to install it. Pick one.

### Option 1: APK (easiest, no Tasker needed)

1. Download the latest `MonoTile.apk` from the [**Releases**](../../releases/latest) page.
2. Install it. Your phone may ask you to allow installs from your browser or file manager.
3. Open **MonoTile** once and **grant root** when your root manager asks.
4. Pull down Quick Settings, tap the **edit** (pencil) button, and drag the **MonoTile** tile into your active tiles.
5. Long-press the tile once to sync it with the current setting. Then tap to toggle.

### Option 2: Tasker project

For people who already use [Tasker](https://tasker.joaoapps.com/) (6.5 or newer) and want to tweak it.

1. Download [`Mono_Audio_QSTile.prj.xml`](Mono_Audio_QSTile.prj.xml) to your phone (open the file on GitHub, then tap **Raw** / the download button).
2. **Import it into Tasker:** open Tasker, long-press the **home icon** (the house tab at the bottom left), choose **Import Project**, and pick the file.
   Tasker's *Beginner Mode* hides this option, so turn it off first in *Preferences → UI*.
3. **Grant root** when Tasker asks for it the first time a task runs.
4. **Add the tile:** pull down Quick Settings, tap the **edit** (pencil) button, and drag the **first Tasker tile** into your active tiles.
5. **Initialise it:** long-press the new tile once (or run the `Mono-tile_sync` task in Tasker). It will relabel itself **Mono** and show the current state.

## How it works

MonoTile is built with Tasker; the APK is the same project exported with Tasker App Factory. It contains three tasks and one profile:

| Item | Type | What it does |
|---|---|---|
| `Mono-tile_tap` | Task | Reads `master_mono`, flips it (0 ↔ 1), writes it back, then updates the tile. Runs when you **tap** the tile. |
| `Mono-tile_sync` | Task | Reads `master_mono` and updates the tile to match. Runs when you **long-press** the tile and when Bluetooth connects. |
| `Mono-off` | Task | Forces `master_mono` to `0` (stereo) and updates the tile. Runs when Bluetooth disconnects. |
| `Mono-bluetooth` | Profile | State: *BT Connected* (any device). Entry task: `Mono-tile_sync`. Exit task: `Mono-off`. |

Under the hood it uses the same setting that the Accessibility toggle changes:

```sh
# read (1 = mono, 0 = stereo)
settings get system master_mono

# toggle
v=$(settings get system master_mono); if [ "$v" = "1" ]; then n=0; else n=1; fi
settings put system master_mono $n
```

You can try these yourself from a root shell to check that your device supports the setting.

## Customising (Tasker project)

- **Don't want mono turned off on Bluetooth disconnect?** Disable the `Mono-bluetooth` profile, or remove its exit task.
- **Only react to one Bluetooth device?** Edit the profile's *BT Connected* state and fill in the device name or address.
- **Already using Tasker's first tile for something else?** In each of the three tasks, change the *Number* field of the **Set Quick Setting Tile** actions to a free tile, then add that tile instead.
- **Different label or icon?** Edit the *Label* / *Icon* fields in the same **Set Quick Setting Tile** actions.

## Troubleshooting

- **The tile does nothing / stays inactive:** make sure root access was granted (check your root manager, e.g. Magisk or KernelSU).
- **The state looks wrong:** long-press the tile to resync it.
- **`settings get system master_mono` prints `null`:** the value has never been set on your device. Toggle mono audio once in Accessibility settings, or just tap the tile (it treats anything other than `1` as off).

## Contributing

Issues and pull requests are welcome, especially reports of which devices and Android versions it works on.

## License

[MIT](LICENSE)
