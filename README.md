
# xfiles

xfiles: Meson Cross-compilation files

## Description

A collection of cross-compilation files for use with Meson. Pretty self-explanatory.
The project tries to provide sane defaults for platforms that need to be compiled
without runtime type checking or exception handling. This simplifies a project's
meson.build file significantly, and makes it more readable.

## Installation

Install in root fs (requires root permissions):
```sh
meson setup build . --prefix=/usr
meson install -C build
```

Install in user fs (does not require permissions):
```sh
meson setup build . --prefix=~/.local
meson install -C build
```

## Examples

Building a project for Arduino:
```sh
meson setup build .    \
  --cross-file=avr     \
  --cross-file=arduino
ninja -C build
```

Building a project for a baremetal ATmega328P:
```sh
meson setup build .       \
  --cross-file=avr        \
  --cross-file=atmega328p
ninja -C build
```

## Included binaries

The cross-files try to include as much of the gcc/binutils toolchain as possible.
Most of these binaries are provided without the toolchain prefix. As such, binaries
like the toolchain specific objcopy can be used with a project file like in the
following example.

```meson
project('simple', 'c')

objcopy = find_program('objcopy')

elf_file = executable('example', 'example.c')

eep_file = custom_target('example.eep',
  input: elf_file,
  output: 'example.eep',
  command: [objcopy, '-O', 'ihex', '-j', '.eeprom',
    '--set-section-flags=.eeprom=alloc,load',
    '--no-chance-warnings', '--change-section-lma',
    '-eeprom=0', '@INPUT@', '@OUTPUT@',
  ])

hex_file = custom_target('example.hex',
  input: elf_file,
  output: 'example.hex',
  command: [objcopy, '-O', 'ihex', '-R', '.eeprom',
    '@INPUT@', '@OUTPUT@',
  ])
```

Some targets also include vendor specific tools such as avrdude.

```meson
avrdude = find_program('avrdude')

run_target('exmaple.upload',
  depends: hex_file,
  command: [avrdude, '-v', '-v', '-v', '-v',
    '-patmega328p', -'carduino', '-P/dev/ttyACM0', '-b115200',
    '-D', '-Uflash:w:@0@:i'.format(hex_file.full_path()),
  ])
```
