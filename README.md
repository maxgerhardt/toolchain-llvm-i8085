# toolchain-llvm-i8085

An LLVM/Clang toolchain for the **Intel 8085** (MCS-85 family), packaged for
[PlatformIO](https://platformio.org/) and used by the
[`platform-intel_mcs85`](https://github.com/maxgerhardt/platform-intel_mcs85)
development platform.

Clang has a native 8085 backend. Code is generated for the `i8085-unknown-elf`
target; append `+undoc` to the target to allow the undocumented 8085 opcodes
(`LDSI`, `LHLX`, `SHLX`, ...).

## Contents

```
bin/            clang, ld.lld, llvm-objcopy, llvm-objdump, llvm-size,
                llvm-ar, llvm-mc, llvm-nm, llvm-readelf
lib/clang/20/   Clang freestanding resource headers (stddef.h, stdarg.h, ...)
sysroot/
  include/      picolibc C headers
  lib/          libc.a, libgcc.a, libgcc-undoc.a, crt objects, picolibc.ld
```

## Standalone usage

```sh
# compile (documented instruction set)
bin/clang --target=i8085-unknown-elf -std=c23 -O2 -ffreestanding -fno-builtin \
          -isystem sysroot/include -c main.c -o main.o

# assemble a startup file
bin/clang --target=i8085-unknown-elf -c crt0.S -o crt0.o

# link
bin/ld.lld -T linker.ld --gc-sections crt0.o main.o -o firmware.elf \
           sysroot/lib/libc.a sysroot/lib/libgcc.a

# to Intel HEX / raw binary
bin/llvm-objcopy -O ihex   firmware.elf firmware.hex
bin/llvm-objcopy -O binary firmware.elf firmware.bin
```

For undocumented instructions, use `--target=i8085-unknown-elf+undoc` and link
`sysroot/lib/libgcc-undoc.a` instead of `sysroot/lib/libgcc.a`.

## Provenance

Built from the 8085 LLVM backend (`llvm-project-i8085`), Clang 20 base,
commit `58e00be9`.

## License

Apache-2.0. LLVM is distributed under the Apache-2.0-with-LLVM-exception license.
