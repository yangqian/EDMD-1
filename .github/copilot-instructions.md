# Project Guidelines

## Code Style

- This repository contains four self-contained C programs in `Cell/`, `Grow/`, `Multi/`, and `Single/`.
- Match the existing C style in the touched variant: global simulation parameters near the top of the main `.c` file, function definitions in the `.c` file, declarations and structs in the matching `.h` file.
- Keep edits minimal and local to the variant being changed. Do not introduce shared abstractions across directories unless explicitly requested.
- Preserve the performance-oriented style of the code. Avoid adding unnecessary allocations, wrappers, or defensive layers in hot paths.

## Architecture

- `Cell/` uses cell-list collision checks.
- `Multi/` uses neighbor lists with multiple scheduled events per particle.
- `Single/` uses neighbor lists with a single scheduled event per particle.
- `Grow/` generates dense initial configurations by growing particles over time until a target packing fraction is reached.
- Each directory builds its own `md` executable and carries its own source, header, Makefile, and `mt19937ar.c` copy.

## Build And Test

- There is no top-level build or test harness.
- Build the affected variant from its own directory with `make`.
- Example commands: `cd Cell && make`, `cd Multi && make`, `cd Single && make`, `cd Grow && make`.
- Validate changes by rebuilding the touched directory at minimum. If behavior changes, run the resulting `./md` executable from that same directory.

## Conventions

- Simulation parameters are configured as globals near the top of each main source file, not through CLI parsing or config files.
- Input snapshots use the text format documented in `README.md`; particle radii are expected to be in units where the maximum radius is `0.5`.
- The code assumes periodic boundary conditions and uses event-driven scheduling with variant-specific data structures. Preserve those assumptions when modifying collision logic or event handling.
- `Grow/mdgrow.c` differs from the other variants: `N` is a compile-time macro there, not a runtime global.
- Makefiles currently use aggressive optimization flags including `-march=native`. Do not change toolchain flags unless the task specifically requires it.