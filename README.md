# State of custom QMK firmware for NuPhy keyboards

NuPhy V2 keyboards are running a version of [QMK firmware](https://qmk.fm/).
QMK is an open-source project and thankfully NuPhy has published changes they made to QMK.

**Goal of this document:** provide in-depth explanation of existing QMK repos for NuPhy keyboards and only some high-level QMK information.

Why you might want to use custom firmware:
- Fixes – which haven't made to official firmware just yet
- Very custom mappings (e.g. switch between Windows and Mac automatically – based on the Num Lock state)

Potential downsides: we are not sure if NuPhy published all of their code, so
- Some fixes from official firmware might be missing
- Battery life might be worse

If you're willing to continue, here's the order of actions:
1. Get the code from one of the repositories
2. (Optional) Change the code
3. Compile and flash

## List of repositories

### [qmk/qmk_firmware](https://github.com/qmk/qmk_firmware)

Supported NuPhy's keyboards: **none**

### [nuphy-src/qmk_firmware](https://github.com/nuphy-src/qmk_firmware)

Supported keyboards:
- Air60 v2
- Air75 v2
- Air96 v2

Inside: NuPhy’s official repo for all keyboard

Fork of [qmk/qmk_firmware](https://github.com/qmk/qmk_firmware) – but as for 2024-02-28 the work of merging the NuPhy's changes back into QMK was quite slow: qmk/qmk_firmware#22751.
Hoping one day they will get it merged.

Differences from official NuPhy firmware **binaries**: fully unknown, but it seems to be substantial, e.g., "improve Bluetooth connection stability" haven't made it to the official open source version yet.

### [jincao1/qmk_firmware](https://github.com/jincao1/qmk_firmware)

Supported keyboards:
- Air75 v2

What's inside:
- Fixes – some were used by NuPhy
- Mods (some are opinionated)
- Latest QMK
- Latest NuPhy

Changes
- Various NuPhy source bug fixes (e.g., **F key modifiers not working in wireless**).
- Fix misplaced LED matrix configurations
- Refactor RF logic to use a RF driver instead of managing through housekeeping
- Fix for stuck keys in wireless mode
- Make RF connect only for 5 seconds before sleeping again. We don't want the LED blinking for 2 minutes if keys are accidentally pressed on wireless mode without a connection.

### [DP19/qmk_firmware](https://github.com/DP19/qmk_firmware/commits/nuphy-air75-mod)

Supported keyboards:
- Air75 v2

What's inside:
- Fixes
- Opinionated mods
- Latest QMK
- Latest NuPhy

Fixes
-  Send reports twice to try and prevent stuck keys / missed keys

### [edykim/qmk_firmware](https://github.com/edykim/qmk_firmware)

Supported keyboards:
- Air75 v2

Initial attempt to pull the latest QMK into the latest NuPhy code. As of 2024-01-07 – feel free to skip unless you want their opinionated mod.

Note:
- Branch *qmk/nuphy-keyboards*: clean state
- Branch *edykim/nuphy-keyboards-mod*: modified by edykim


### [adi4086/qmk_firmware](https://github.com/adi4086/qmk_firmware)

Supported keyboards:
- Air75 v2
- Air96 v2

First custom firmware for Air96 v2

Fixes
- New algorithm to prevent unnecessary EEPROM writes when you change light settings
- Pulled fixes from jincao1
- Opinionated modifications

### [ablasik/nuphy-qmk](https://github.com/ablasik/nuphy-qmk)

Supported keyboards:
- Air60 v2
- Air75 v2
- Air96 v2

Continues adi4086's firmware (including jincao1's fixes) on the latest QMK. As of 2026-10, upstream QMK is merged weekly.

What's inside:
- Latest QMK and ChibiOS, with QMK's and ChibiOS's own LED, EEPROM (wear leveling) and power code instead of NuPhy's copies
- Fix for freezes and phantom keypresses on current QMK (the RF module's UART ended up on matrix pins after a QMK change)
- Mac/Win remembered per connection, OS detection on the cable
- Prebuilt releases (`.bin`, VIA JSON, Quick Guide PDF) built by CI
- Air75 v2 and Air60 v2 not yet tested on hardware as of 2026-10

## QMK: Tooling

Read this section and then use the [QMK documentation](https://docs.qmk.fm) as further QMK reference.

### QMK CLI
QMK provides a CLI that helps with common tasks
- Cloning (potentially custom) repo
- Compiling
- Flashing
- Debugging

OS support:
- Linux: yes
- Mac:
   - Intel processor: yes
   - Apple Silicon: installation takes 2 hours
- Windows: yes

**Note**: It installs a lot of stuff to your system, so maybe use:

### Docker + QMK Toolbox

**Alternatively**, people with a bit of programming background will be comfortable using
- Git to clone the repo (use `--recurse-submodules` !!!)
- [Docker to compile code](https://github.com/qmk/qmk_firmware/blob/master/docs/getting_started_docker.md)
- [QMK Toolbox](https://github.com/qmk/qmk_toolbox/releases) to flash and debug. (**Mac and Windows only!**)


## QMK: Anatomy of the repo

QMK can be roughly divided into **core** and **keyboards**. We are interested in **keyboards**.

**keyboards** are located under `./keyboards/<company>/<model>/<submodel>`, e.g. `./keyboards/nuphy/air75_v2/ansi` will be for NuPhy Air75 V2.

There you have
- Keymaps – define multiple "layers" which can be switched, most commonly via an `Fn` key. Every keyboard might have several keymaps, and only one can be flashed at the time. E.g., for Air75 v2 two keymaps are provided:
  - `via` – supports [VIA Configurator](https://www.caniusevia.com/) for UI-based configuration
  - `default` – doesn't support VIA – as of 2024-01-07 the only other difference is that it supports showing battery level using `Fn + B`
- Keyboard's low-level code – likely you don't want to change it
