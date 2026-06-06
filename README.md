# DAX2 ZMK Configuration

ZMK firmware configuration for DAX2 keyboard with a local Nix-based build environment.

## Features

- **Nix-based development environment** - Reproducible local builds with `flake.nix`
- **No local Docker dependency** - Native Nix toolchain for development
- **Just-based workflow** - Simple commands for setup, build, flash, and cleanup
- **Official ZMK GitHub Actions build** - CI stays aligned with the reference configuration

## Prerequisites

### Required

- [Nix](https://nixos.org/download.html) with flakes enabled

### Flakes Setup

If flakes are not enabled yet, add this to `~/.config/nix/nix.conf`:

```text
experimental-features = nix-command flakes
```

## Quick Start

### 1. Clone and Enter Directory

```bash
git clone https://github.com/wadackel/zmk-config-dax2.git
cd zmk-config-dax2
```

### 2. Enter Nix Development Shell

```bash
nix develop
```

### 3. Initialize Workspace

```bash
just setup
```

### 4. Build Firmware

```bash
just build
```

Build artifacts will be in `build/`:

- `build/dax2_R.uf2` - Right hand with trackball and ZMK Studio
- `build/dax2_L.uf2` - Left hand
- `build/settings_reset.uf2` - Settings reset utility

## Common Commands

Run these commands inside the Nix development environment (`nix develop`).

### Build Commands

```bash
# Build all targets
just build

# Build a specific target
just build-target dax2_R
just build-target dax2_L
just build-target settings_reset
```

### Flash Commands

```bash
# Flash the right hand target
just flash dax2_R

# Flash the left hand target
just flash dax2_L
```

`just flash` expects the XIAO bootloader volume at `/Volumes/XIAO-SENSE` and uses `build/<target>.uf2` when available.

### Quick Iteration

```bash
# Build and flash the right hand target
just quick

# Build and flash the left hand target
just quick-left
```

### Maintenance

```bash
# Clean build artifacts
just clean

# Reinitialize or update the west workspace
just setup

# Remove generated workspace and build state
just pristine
```

### Non-Interactive Execution

You can run commands without entering an interactive shell:

```bash
nix develop --command just setup
nix develop --command just build
nix develop --command just build-target dax2_R
```

This is useful for scripts and automated tools.

## Project Structure

```text
.
├── flake.nix              # Nix development environment definition
├── flake.lock             # Locked dependency versions
├── justfile               # Common setup, build, flash, and cleanup commands
├── build.yaml             # Official ZMK workflow build matrix
├── config/                # ZMK configuration files
│   ├── west.yml           # West manifest
│   └── dax2.keymap        # Keymap definition
├── boards/                # Board definitions
│   └── shields/dax2/      # DAX2 shield configuration
├── scripts/               # Build and utility scripts
│   ├── setup.sh           # West workspace initialization
│   ├── build-nix.sh       # Build all firmware targets
│   ├── build-target.sh    # Build one firmware target
│   └── flash.sh           # Copy UF2 firmware to the bootloader volume
└── build/                 # Build output (git-ignored)
```

## Troubleshooting

### "Must run inside nix develop environment"

Enter the Nix development shell before running `just setup`, `just build`, or `just build-target`:

```bash
nix develop
```

### West workspace is not initialized

Initialize or refresh the workspace:

```bash
just setup
```

For a full local rebuild of generated workspace state:

```bash
just pristine
just setup
```

### Build artifact not found when flashing

Build the target before flashing it:

```bash
just build-target dax2_R
just flash dax2_R
```

## Custom Modules

This configuration uses the following custom ZMK modules:

- **zmk-pmw3610-driver** - Trackball sensor driver (PMW3610)
- **zmk-rgbled-widget** - RGB LED widget support

These modules are fetched during `just setup` through `config/west.yml`.

## GitHub Actions

GitHub Actions use the official ZMK reusable workflow, matching the reference repository configuration. Local builds use Nix for development.
