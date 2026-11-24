# DSperate for muOS (Andromeda and Supernova)

This fork packages [DSperate](https://github.com/beebono/DSperate), beebono's
Nintendo DS and DSi emulator, as a one-file installer for
[muOS](https://muos.dev) on the Anbernic RG35XX family (Allwinner H700).

The emulator is upstream's own code and its own prebuilt binaries. This fork
adds only the muOS side: a launcher, a starting config and a core definition,
so that DSperate shows up as a standalone emulator for the Nintendo DS system.

Upstream's README, with the full feature list, is in
[README.upstream.md](README.upstream.md).

## Status

| DSperate | muOS | Package | On device |
|---|---|---|---|
| v4.0.1 | 2606.0 Andromeda | `dsperate-v4.0.1-andromeda.muxzip` | ✔ passed (RG35XX Pro) |
| v4.0.1 | 2612 Supernova | `dsperate-v4.0.1-supernova.muxzip` | ✔ passed (RG35XX Pro) |

Both packages were tested on an RG35XX Pro (2026-11-24) and passed all of these
checks:
- a DS game boots;
- an in-game save survives a reboot;
- an existing save from an older version still loads;
- saving and loading a state works;
- a DSi game runs at an acceptable speed.

Other RG35XX models (Plus, H, SP, 2024) use the same H700 SoC and should behave
the same, but only the Pro has been tested. Handhelds with other chips are not
covered.

## Which package

- **`-andromeda`**: muOS 2606.0 Andromeda (final release). Builds of Andromeda older
  than October 2026 used a different core-list layout and are not supported.
- **`-supernova`**: muOS 2612 Supernova.

The two are not interchangeable. Each muOS release keeps its core definitions in
a different place and format.

## Install

1. Download the package for your muOS release from
   [Releases](https://github.com/fragilesilver/DSperate/releases).
2. Copy the `.muxzip` to `ARCHIVE/` on SD1.
3. On the device, open **Applications → Archive Manager** and install it.
4. **Reboot.** muOS reads core definitions at boot.
5. In the Nintendo DS ROM folder, set the core to **DSperate - Standalone**
   (for one game, or for the whole folder).

Reinstalling or updating never overwrites your settings. The config is created
from the packaged defaults on the first launch only.

## What it installs

| Package section | Lands at | Contents |
|---|---|---|
| `emulator/` | `/opt/muos/share/emulator/dsperate/` | `dsperate`, `dsperate-headless`, `dsperate.ini.default` |
| `script/` | `/opt/muos/script/launch/ext-dsperate.sh` | launcher |
| `info/` | `SD1/MUOS/info/manifest/` | DS core definition: `core.json` (Andromeda) or `core/Nintendo/Nintendo DS.json` (Supernova) |

## Files on the device

| What | Where |
|---|---|
| Config | `/opt/muos/share/emulator/dsperate/.config/dsperate/dsperate.ini` |
| Battery saves | `MUOS/save/file/DSperate/` |
| Save states | `MUOS/save/state/DSperate/` |
| Screenshots | `MUOS/screenshot/` |
| BIOS, firmware, cheats (optional) | `MUOS/bios/nds/` (`bios9.bin`, `bios7.bin`, `firmware.bin`, `usrcheat.dat`) |
| Last run's log | `SD1/MUOS/log/dsperate.txt` |

The `MUOS/...` paths are on SD1. Games run without BIOS or firmware dumps.
A set dumped from your own DS adds the firmware boot screen and cycle-exact BIOS
timing. DSi mode needs dumps from your own DSi. None are included.

## Notes

- **Video goes straight to the framebuffer** (`[video] sink = fbdev`). This skips
  the SDL2 that muOS ships for its own frontend.
- **Face buttons.** muOS's default *Retro* control scheme already lays the
  buttons out Nintendo-style, so the starting config maps the face buttons
  one-to-one. If you switch the content's control scheme to *Modern*, remove the
  `[pad]` block from `dsperate.ini` to get DSperate's own defaults back.
- **After a muOS update, install the package again.** The core definition on
  SD1 replaces muOS's own (the DS system's on Supernova, the whole core list on
  Andromeda). A stale copy can hide cores that a muOS update adds.
- **DSi games** use DSperate's exact timing model by default. Setting
  `[emu] timing` in `dsperate.ini` picks another model; DSperate then shows a
  notice at launch.
- **A battery save that can't be read stops the launch** (new in DSperate
  4.0), so a damaged save isn't silently replaced. Check the log above.

## Uninstall

Delete `/opt/muos/share/emulator/dsperate/` and
`/opt/muos/script/launch/ext-dsperate.sh`. Then delete the DS core definition
this package added under `SD1/MUOS/info/manifest/` (on Andromeda, the whole
`core.json` there) and reboot. Your saves in `MUOS/save/` are left alone.

## Credits and licence

DSperate is by beebono and contributors. This fork follows upstream
releases and does not change the emulator itself. Report emulator bugs
[upstream](https://github.com/beebono/DSperate/issues), and muOS packaging
problems here. muOS is by the [MustardOS](https://github.com/MustardOS) team.
This fork is not affiliated with either project.

GPL-3.0, as upstream; see [LICENSE](LICENSE).
