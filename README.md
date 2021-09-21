
# xfiles

xfiles: Meson Cross-compilation files

## Description

A collection of cross-compilation files for use with Meson. Pretty self-explanatory.

## Installation

```sh
meson setup build . --prefix=~/.local #install as user
meson setup build . --prefix=/usr     #install as root
meson install -C build
```

## Examples

Building a project for Arduino:
```sh
meson . build --cross-file=arduino
ninja -C build
```
