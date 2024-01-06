# State of custom QMK firmware for Nuphy keyboards

Nuphy V2 keyboards are running a version of [QMK firmware](https://qmk.fm/).
QMK is an open-source project and thankfully Nuphy has published changes they made to QMK.

**Goal of this doc:** provide in-depth explanation of existing QMK repos for Nuphy keyboards and only some high-level QMK info.

Why you might want to use custom firmware:
- Fixes – which haven't made to official firmware just yet
- Very custom mappings (e.g. switch between Win and Mac automatically – based on NumLock state)

Potential downsides: we are not sure if Nuphy published all of their code, so
- Some fixes from official firmware might be missing
- Battery life might be worse

If you're willing to continue, here's the order of actions:
1. Get the code from one of the repositories
2. (Optional) Change the code 
3. Compile and flash

## List of repositories

### [qmk/qmk_firmware](https://github.com/qmk/qmk_firmware)

Supported Nuphy's keyboards: **none**

### [nuphy-src/qmk_firmware](https://github.com/nuphy-src/qmk_firmware)

Supported keyboards:
- Air60 v2
- Air75 v2
- Air96 v2

Inside: Nuphy’s official repo for all keyboard

Fork of [qmk/qmk_firmware](https://github.com/qmk/qmk_firmware) – but as for 2024-01-07 the work of merging the Nuphy's changes back into QMK was quite slow: qmk/qmk_firmware#22751.
Hoping one day they will get it merged.

Differences from official Nuphy firmware **binaries**: unknown

### [jincao1/qmk_firmware](https://github.com/jincao1/qmk_firmware)

Supported keyboards:
- Air75 v2

What's inside:
- Fixes
- Mods (some are opinionated)
- Latest QMK
- Latest Nuphy

Changes
- Various Nuphy source bug fixes (e.g., **F key modifiers not working in wireless**).
- Fix misplaced LED matrix configurations
- Refactor RF logic to use a RF driver instead of managing through housekeeping
- _Attempted_ fixes for stuck keys in wireless mode.
- Make RF connect only for 5 seconds before sleeping again. Don't want the LED blinking for 2 minutes if keys are accidentally pressed on wireless mode without a connection.


### [edykim/qmk_firmware](https://github.com/edykim/qmk_firmware)

Supported keyboards:
- Air75 v2

Initial attempt to pull latest QMK into latest Nuphy code. As of 2024-01-07 – feel free to skip unless you want their opinionated mod.

Note:
- Branch qmk/nuphy-keyboards : clean state
- Branch edykim/nuphy-keyboards-mod : modded by edykim

## QMK: Tooling

Read this section and then use [QMK docs](https://docs.qmk.fm) as further QMK reference.

### QMK CLI
QMK provides CLI that helps with common tasks
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

**Note**: It installs a lot of stuff to your system so maybe use:

### Docker + QMK Toolbox

**Alternatively**, people with a bit of programming background will be comfortable using 
- Git to clone repo (use `--recurse-submodules` !!!)
- [Docker to compile code](https://github.com/qmk/qmk_firmware/blob/master/docs/getting_started_docker.md)
- [QMK Toolbox](https://github.com/qmk/qmk_toolbox/releases) to flash and debug. (**Mac and Windows only!**)


## QMK: Anatomy of the repo

QMK can be roughly divided into **core** and **keyboards**. We are interested in **keyboards**.

**keyboards** are located under `./keyboards/<company>/<model>/<submodel>`, e.g. `./keyboards/nuphy/air75_v2/ansi` will be for Nuphy Air75 V2.

There you have 
- Keymaps – define multiple "layers" which can be switched, most commonly via `Fn` key. Every keyboard might have several keymaps, only one can be flashed at the time. E.g. for Air75 v2 two keymaps are provided:
  - `via` – supports [VIA Configurator](https://www.caniusevia.com/) for UI-based config
  - `default` – doesn't support VIA – as of 2024-01-07 the only other difference is that it supports showing battery level using `Fn + B`
- Keyboard's low-level code – likely you don't want to change it

## QMK: Workflow

- Make a code change
- Compile: outputs a `.bin` file
- Flash
