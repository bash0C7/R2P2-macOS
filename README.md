# R2P2-macOS

A staging harness for building and running PicoRuby with Darwin-native
capabilities on a macOS host.

## What this is

PicoRuby itself builds on macOS as a POSIX host — for that alone, you do not
need this repository. R2P2-macOS exists because Darwin-native mrbgems need:

1. a build config that opts the gem in on Darwin, separate from upstream's
   `build_config/default.rb`,
2. macOS-specific prerequisites pinned (Xcode CLT, Homebrew openssl@3,
   Swift 6.3+),
3. a venue where the per-host build config lives until the Darwin-native
   mrbgem and a matching `build_config/*-darwin.rb` are accepted upstream.

Once that lands in `picoruby/picoruby`, this repository's job ends. R2P2-ESP32
is the comparable harness on the ESP-IDF axis; that one is permanent because
ESP-IDF is a substantial external build system. macOS has no such external
system — Darwin-native code lives inside the picoruby tree as mrbgems with
their own `mrbgem.rake` (self-compiles Swift, links frameworks, etc.). This
harness is a thin wrapper, not a long-lived host port.

## Setup

```
brew install openssl@3            # networking gembox links ssl/crypto
xcode-select --install            # clang + Swift toolchain
# Ruby — any ambient install (rbenv / asdf / system) >= 2.7
```

```
rake check                        # verifies the above
```

## Choosing what to build

The picoruby tree to build is selectable by env:

```
PICORUBY_REPO   default: https://github.com/picoruby/picoruby.git
PICORUBY_REF    default: master
MRUBY_CONFIG    optional; if unset, the picoruby tree's own default.rb is used
```

### Standard build

```
rake build                        # ./build/host/bin/{r2p2,picoruby}
rake run                          # r2p2 shell
rake run APP=path/to.rb           # run a Ruby file on the picoruby runner
```

### Example: picoruby-ble Darwin port (CoreBluetooth)

The picoruby-ble Darwin port is the present macOS-dependent capability this
harness serves — it uses CoreBluetooth, which only exists on Darwin. As of
2026-06-20 the port lives at `https://github.com/bash0C7/picoruby.git` on
branch `picoruby-ble-darwin-port`, awaiting upstream merge. Point
`PICORUBY_REPO`/`PICORUBY_REF` at it and select the bundled build config:

```
PICORUBY_REPO=https://github.com/bash0C7/picoruby.git \
PICORUBY_REF=picoruby-ble-darwin-port \
MRUBY_CONFIG=$(pwd)/build_config/r2p2-picoruby-darwin.rb \
rake setup build
```

`build_config/r2p2-picoruby-darwin.rb` mirrors picoruby's per-target naming
convention (parallel to `build_config/r2p2-picoruby-pico2.rb` upstream): a
Darwin host build that opts the `picoruby-ble` gem in. Tests and design docs
for the port live with the port itself under
`mrbgems/picoruby-ble/ports/darwin/` in the picoruby tree.

To rebuild after editing the picoruby tree (e.g. switching branches):

```
PICORUBY_REPO=... PICORUBY_REF=... rake refresh build
```

## Layout

```
R2P2-macOS/
  Rakefile                          setup / check / build / run / clean / clobber
  build_config/
    r2p2-picoruby-darwin.rb         Darwin host build config (see Example above)
  vendor/picoruby/                  fetched by rake setup (gitignored)
  build/                            build output, MRUBY_BUILD_DIR (gitignored)
```
