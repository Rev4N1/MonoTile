# Mono Audio Quick Settings Tile for Tasker

A one-tap **Mono Audio** toggle in your Android Quick Settings panel, built with [Tasker](https://tasker.joaoapps.com/).

Android hides the mono audio switch deep inside *Settings → Accessibility → Audio*. That's a pain if you sometimes listen with a single earbud or share a pair with someone. This Tasker project puts the switch one swipe away and keeps it in sync with the real system setting.

## Features

- **One-tap toggle** – tap the tile to switch mono audio on or off.
- **Accurate tile state** – the tile shows active or inactive based on the real system value, not a guess.
- **Long-press to resync** – long-press the tile to re-read the setting if you changed it somewhere else (for example in Accessibility settings).
- **Bluetooth aware** – the tile resyncs when a Bluetooth device connects, and mono audio is **turned off automatically when Bluetooth disconnects**, so your phone speaker goes back to stereo.

## Requirements

| | |
|---|---|
| Android | Any version with the system *Mono audio* accessibility option (Android 7+ has the Quick Settings tile API Tasker needs) |
| Tasker | 6.5 or newer (the project was exported from 6.5.11) |
| **Root** | **Required.** The shell commands run as root, because `master_mono` is a hidden system setting that normal apps can't write. |

## Installation

1. **Download** [`Mono_Audio_QSTile.prj.xml`](Mono_Audio_QSTile.prj.xml) to your phone (open the file on GitHub, then tap **Raw** / the download button).
2. **Import it into Tasker:** open Tasker, long-press the **home icon** (the house tab at the bottom left), choose **Import Project**, and pick the file.
   Tasker's *Beginner Mode* hides this option, so turn it off first in *Preferences → UI*.
3. **Grant root** when Tasker asks for it the first time a task runs.
4. **Add the tile:** pull down Quick Settings, tap the **edit** (pencil) button, and drag the **first Tasker tile** into your active tiles.
5. **Initialise it:** long-press the new tile once (or run the `Mono-tile_sync` task in Tasker). It will relabel itself **Mono** and show the current state.

That's it. Tap to toggle.

## How it works

The project contains three tasks and one profile:

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

You can try these yourself from `adb shell` to check that your device supports the setting.

## Customising

- **Don't want mono turned off on Bluetooth disconnect?** Disable the `Mono-bluetooth` profile, or remove its exit task.
- **Only react to one Bluetooth device?** Edit the profile's *BT Connected* state and fill in the device name or address.
- **Already using Tasker's first tile for something else?** In each of the three tasks, change the *Number* field of the **Set Quick Setting Tile** actions to a free tile, then add that tile instead.
- **Different label or icon?** Edit the *Label* / *Icon* fields in the same **Set Quick Setting Tile** actions.

## Troubleshooting

- **The tile does nothing / stays inactive:** check that Tasker has root access, and run `Mono-tile_tap` from inside Tasker to see the error.
- **The state looks wrong:** long-press the tile to resync it.
- **`settings get system master_mono` prints `null`:** the value has never been set on your device. Toggle mono audio once in Accessibility settings, or just tap the tile (it treats anything other than `1` as off).

## Contributing

Issues and pull requests are welcome, especially reports of which devices and Android versions it works on.

## License

[MIT](LICENSE)
