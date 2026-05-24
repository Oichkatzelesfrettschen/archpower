# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ArchPOWER is a port of Arch Linux to PowerPC/POWER platforms. This repository contains PKGBUILD files for building packages on PowerPC architectures (powerpc, powerpc64, powerpc64le) and the espresso target.

- Website: https://archlinuxpower.org
- Package repository: https://repo.archlinuxpower.org

## Repository Structure

The repository contains ~1770 package directories. Most packages are at the top level, but some are organized into category subdirectories:

**Category directories with nested packages:**
- `cross-compilers/` - Cross-compilation toolchains for various architectures (aarch64, arm, hppa, m68k, parisc, powerpc variants, riscv64, x86_64)
- `go/` - Go language packages (docker, containerd, gitea, kubernetes tooling)
- `java/` - Java/JVM packages (openjdk versions, gradle, maven)
- `kde/` - KDE applications
- `kf5/` - KDE Frameworks 5
- `kf6/` - KDE Frameworks 6
- `nodejs/` - Node.js packages
- `ocaml/` - OCaml packages
- `perl/` - Perl packages
- `plasma/` - KDE Plasma desktop
- `python/` - Python packages
- `qt5/` - Qt 5 libraries
- `qt6/` - Qt 6 libraries
- `rocm/` - AMD ROCm packages
- `ruby/` - Ruby packages
- `rust/` - Rust toolchain (with PowerPC-specific patches)
- `xorg/` - X.org packages
- `gnome/`, `lxde/`, `lxqt/`, `xfce4/`, `tde/` - Desktop environments
- `games/`, `k8s/`, `dotnet/`, `riscv64/` - Other categories

## Building Packages

Standard Arch Linux package building with makepkg:

```sh
# Build a single package
cd <package-dir>
makepkg -s

# Build without installing dependencies (if already present)
makepkg

# Build and install
makepkg -si
```

## Architecture Conventions

PKGBUILDs in this repo specify multiple architectures in the `arch=()` array:
- `x86_64` - Standard x86-64
- `powerpc64le` - 64-bit PowerPC little-endian (POWER8+)
- `powerpc64` - 64-bit PowerPC big-endian
- `powerpc` - 32-bit PowerPC
- `espresso` - Custom PowerPC target
- `any` - Architecture-independent

Example from a typical PKGBUILD:
```bash
arch=(x86_64 powerpc64le powerpc64 powerpc espresso)
```

The first comment line identifies the POWER maintainer:
```bash
# POWER Maintainer: Alexander Baldeck <alex.bldck@gmail.com>
```

## PowerPC-Specific Patches

Many packages require architecture-specific patches for PowerPC support. Common patterns:
- Big-endian fixes (e.g., `big-endian-*.patch`, `hotfix-bigendian-*.patch`)
- Atomic operations (e.g., `powerpc-atomics.patch`, `*-atomic.patch`)
- ELFv2 ABI patches (e.g., `elfv2-ppc64-*.patch`, `*-elfv2-ppc64.patch`)
- VSX/Altivec SIMD adaptations

Look in package directories for `.patch` files when troubleshooting build issues.

## Cross-Compilation Toolchains

The `cross-compilers/` directory contains full toolchain packages for cross-compiling to various architectures. Each target has:
- `*-binutils` - Binary utilities
- `*-gcc-stage1/stage2/gcc` - GCC compiler stages
- `*-glibc-headers/glibc` - C library
- `*-linux-api-headers` - Kernel headers
- `*-libxcrypt` - Crypt library
- `*-pacman` - Pacman configured for the target

## Common PKGBUILD Patterns

Architecture-specific build skips in check() functions:
```bash
check() {
  case "${CARCH}" in
    powerpc*) return 0 ;;
  esac
  # run tests...
}
```

Conditional dependencies or build options based on architecture are common.
