# Advantage 360 Pro — ZMK Keymap

[![Keymap](keymap-drawer/adv360.svg)](keymap-drawer/adv360.svg)

The diagram above is generated from [`config/adv360.keymap`](config/adv360.keymap) on every push by
[keymap-drawer](https://github.com/caksoylar/keymap-drawer) and committed back to this repo, so it is
never out of date with the firmware. Small legends under a keycap are the key's *hold* behavior.

## Layers

| # | Name | Reached by |
| --- | --- | --- |
| 0 | Base | — |
| 1 | Keypad | hold `F`, or `tog 1` from the top row |
| 2 | Nav | hold `D` |
| 3 | Symbols | hold `S` |
| 4 | Mod | `mo 4` (upper inner key, all layers) |
| 5 | Function | `mo 5` (bottom-right) |

Home-row mods are on `X` `C` `V` (left: GUI/ALT/CTRL) and `M` `,` `.` (right: CTRL/ALT/GUI), built with
[urob's zmk-helpers](https://github.com/urob/zmk-helpers) and positional hold-tap so same-hand rolls
don't fire a modifier.

Exact key positions for combos are documented in [assets/key-positions.md](assets/key-positions.md).

## Regenerating the diagram

CI handles this automatically ([`.github/workflows/draw-keymap.yml`](.github/workflows/draw-keymap.yml)),
but to preview locally:

```shell
pipx install keymap-drawer                       # or: uv tool install keymap-drawer
git clone --depth 1 -b v0.3 https://github.com/urob/zmk-helpers.git

keymap -c keymap_drawer.config.yaml parse -z config/adv360.keymap > keymap-drawer/adv360.yaml
keymap -c keymap_drawer.config.yaml draw keymap-drawer/adv360.yaml \
    -d config/boards/arm/adv360/adv360-layouts.dtsi -o keymap-drawer/adv360.svg
```

`zmk-helpers` must be checked out at the repo root — [`keymap_drawer.config.yaml`](keymap_drawer.config.yaml)
adds `zmk-helpers/include` to the preprocessor search path, matching where `west update` puts it in CI.

Layer names in the diagram come from the `display-name` property of each layer; legends and colors are
tunable in `keymap_drawer.config.yaml`.

## Building the firmware

Via GitHub Actions: push a commit, then download the artifact from the build job.

Locally, with Docker or Podman plus `make`:

```shell
make            # both halves
make left       # left half only
make clean      # remove container and built firmware
```

Output lands in `firmware/`, timestamped.

## Flashing

1. Connect the **left** half via USB, press `Mod`+`macro1` to enter bootloader; it mounts as a USB drive.
2. Copy `left.uf2` to it. It will disconnect.
3. Power off both halves, then turn the left half on with its switch.
4. Connect the **right** half via USB, press `Mod`+`macro3`, copy `right.uf2`.
5. Unplug the right half and turn it back on.

Both halves also have physical reset buttons — see section 2.7 of the
[User Manual](https://kinesis-ergo.com/wp-content/uploads/Advantage360-ZMK-KB360-PRO-Users-Manual-v3-10-23.pdf).

`Mod`+`V` types a build stamp of the form `YYYYMMDD-XXXX-YYYYYY` (date, branch prefix, commit hash).

## Upstream documentation

This is a fork of [KinesisCorporation/Adv360-Pro-ZMK](https://github.com/KinesisCorporation/Adv360-Pro-ZMK),
which builds against [a Kinesis fork of ZMK](https://github.com/ReFil/zmk/tree/adv360-z3.5) rather than
upstream ZMK. Their README covers NKRO, BLE battery reporting, indicator LED colors, the full layer-color
table, and beta testing; [`CHANGELOG.md`](CHANGELOG.md) and [`UPGRADE.md`](UPGRADE.md) are unchanged here.

Hardware support: <https://kinesis-ergo.com/support/kb360pro/>
