
# xfiles

xfiles: Meson Cross-compilation files

## Description

A collection of cross-compilation files for use with Meson. Pretty self-explanatory.

## Examples

```sh
cd path_to_project_root
meson . build --cross-file=*.xfile
ninja -C build
```
