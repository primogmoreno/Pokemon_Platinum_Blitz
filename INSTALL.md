# Pokemon Platinum Blitz — Build Setup

## Overview

This project forks `pret/pokeplatinum`, the byte-for-byte decompilation of Pokemon Platinum. Changes are made in version-controlled C source and data files, compiled with the Metrowerks toolchain under WSL2, and distributed as an `.xdelta` patch.

---

## IMPORTANT: Project Must Live in WSL2's Native Filesystem

The build toolchain (Meson, mwccarm, arm-none-eabi) **must run inside WSL2** and the project **must be in WSL2's native Linux filesystem** — NOT in a Windows path like `C:\Users\...`. Accessing Windows drives from WSL2 via `/mnt/c/...` is 10–20x slower and causes file-permission errors with the build tools.

| What | Where |
|---|---|
| Project source code | `~/Pokemon_Platinum_Blitz/` (WSL2 Ubuntu) |
| Base ROM | `~/platinum.nds` (WSL2, outside the repo) |
| mwccarm + NitroSDK | `~/mwccarm/` and `~/NitroSDK/` (WSL2, outside the repo) |
| File editing from Windows | VS Code Remote-WSL extension — see Step 5 below |
| View files in Windows Explorer | `\\wsl$\Ubuntu\home\<username>\Pokemon_Platinum_Blitz` |

---

## Step 1 — Enable WSL2 and Install Ubuntu

WSL2 is already installed on this machine. To verify:
```powershell
# In Windows PowerShell:
wsl --list --verbose
```
You should see an Ubuntu distribution at VERSION 2. If not, run:
```powershell
wsl --install
```
and restart.

---

## Step 2 — Configure Git in WSL2

Open the Ubuntu terminal app (or run `wsl` in PowerShell). Configure git identity:
```bash
git config --global user.name "primogmoreno"
git config --global user.email "primogmoreno@gmail.com"
```

Set up SSH or personal access token for GitHub pushes from WSL2:
```bash
# Generate SSH key in WSL2
ssh-keygen -t ed25519 -C "primogmoreno@gmail.com"
cat ~/.ssh/id_ed25519.pub
# Copy the output and add it to GitHub: Settings → SSH and GPG keys → New SSH key
```

---

## Step 3 — Clone the Project into WSL2

All work happens at `~/Pokemon_Platinum_Blitz` inside WSL2.

```bash
# In WSL2 terminal:
cd ~
git clone git@github.com:primogmoreno/Pokemon_Platinum_Blitz.git
cd Pokemon_Platinum_Blitz
git submodule update --init pokeplatinum
```

This clones the wrapper repo and initializes the `pokeplatinum/` submodule (the full pret/pokeplatinum decompilation source).

---

## Step 4 — Install Build Dependencies in WSL2

```bash
sudo apt update && sudo apt install -y \
    build-essential \
    git \
    python3 python3-pip \
    perl \
    ninja-build \
    pkg-config \
    libpng-dev \
    p7zip-full \
    xdelta3
```

Install Meson 1.5+ (the apt package may be too old):
```bash
pip3 install --user meson
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
meson --version   # should print 1.5.x or higher
```

Install devkitARM (arm-none-eabi toolchain):
```bash
wget https://apt.devkitpro.org/install-devkitpro-pacman
chmod +x ./install-devkitpro-pacman
sudo ./install-devkitpro-pacman
sudo dkp-pacman -S devkitARM
```

Add devkitARM to PATH:
```bash
echo 'export DEVKITPRO=/opt/devkitpro' >> ~/.bashrc
echo 'export DEVKITARM=/opt/devkitpro/devkitARM' >> ~/.bashrc
echo 'export PATH=$DEVKITARM/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
arm-none-eabi-gcc --version   # verify
```

---

## Step 5 — Install VS Code Remote-WSL (for editing from Windows)

In Windows, install the **Remote - WSL** extension for VS Code:
- Open VS Code → Extensions → search "Remote - WSL" → Install
- Then: `code ~/Pokemon_Platinum_Blitz` from the WSL2 terminal, or click "Open a Remote Window" in VS Code

This gives you full VS Code editing capabilities directly on the WSL2 filesystem, with no performance penalty.

---

## Step 6 — Get Proprietary Build Tools (PRET Discord)

> These tools are required to build pret/pokeplatinum. They cannot be distributed.

1. Join the PRET Discord: https://discord.gg/d5sBgFF
2. Go to the `#pokediamond` channel and find the pinned messages
3. Download:
   - `mwccarm.zip` — Metrowerks C Compiler for ARM (required by the game's original build system)
   - `NitroSDK-3_2-060901.7z` — Nintendo DS SDK headers and libraries

Extract them into your WSL2 home directory:
```bash
# In WSL2 (copy the downloaded files from Windows first):
cp /mnt/c/Users/primo/Downloads/mwccarm.zip ~/
cp /mnt/c/Users/primo/Downloads/NitroSDK-3_2-060901.7z ~/

unzip ~/mwccarm.zip -d ~/mwccarm
7z x ~/NitroSDK-3_2-060901.7z -o$HOME/NitroSDK
```

Initialize the mwccarm license (run the Windows executable once, from Windows — not WSL2):
```
# In Windows: double-click mwccarm.exe to initialize license.dat
```

Then in WSL2, set the license path:
```bash
echo 'export LM_LICENSE_FILE="$HOME/mwccarm/license.dat"' >> ~/.bashrc
source ~/.bashrc
```

---

## Step 7 — Verify pokeplatinum Submodule

The `pokeplatinum/` submodule is pre-configured in this repository. If you ran `git submodule update --init pokeplatinum` in Step 3, it is already initialized. Verify it is present:

```bash
ls ~/Pokemon_Platinum_Blitz/pokeplatinum/src   # should list source files
git -C ~/Pokemon_Platinum_Blitz submodule status  # should show a commit hash
```

> **Maintainer note:** If you are setting up the repository for the first time as the project owner, you need to fork `pret/pokeplatinum` at https://github.com/pret/pokeplatinum, then add it as a submodule:
> ```bash
> cd ~/Pokemon_Platinum_Blitz
> git submodule add git@github.com:primogmoreno/pokeplatinum.git pokeplatinum
> git commit -m "Add pokeplatinum submodule"
> git push origin main
> ```

---

## Step 8 — Build pokeplatinum (Baseline Verification)

```bash
cd ~/Pokemon_Platinum_Blitz/pokeplatinum
meson setup build \
    --cross-file cross/arm9.ini \
    -Dmwccarm=$HOME/mwccarm/mwccarm.exe \
    -DNitroSDK=$HOME/NitroSDK
meson compile -C build
```

Verify the output matches the vanilla ROM:
```bash
sha1sum build/platinum.nds
# Should match the hash in pokeplatinum/rom.sha1
```

A matching SHA1 means your toolchain is set up correctly. Any Blitz modifications will change this hash — that's expected.

---

## Step 9 — Place the Base ROM

Your base ROM must be kept **outside** the repository (it's gitignored). Copy it from Windows:
```bash
cp "/mnt/c/Users/primo/OneDrive/Documents/Coding Projects/Pokemon_Platinum_Blitz/platinum_rom.nds" ~/platinum.nds
```

Verify it's the correct version:
```bash
sha256sum ~/platinum.nds
# Expected: fbce4c4def0c7797f8dd238a3d7a5e48b4a7e3abd86890ac65f6321cef781bdb
```

---

## Creating a Distributable Patch

Once changes are built, create an `.xdelta` patch against the vanilla ROM:
```bash
xdelta3 -e -s ~/platinum.nds ~/Pokemon_Platinum_Blitz/pokeplatinum/build/platinum.nds \
    ~/Pokemon_Platinum_Blitz/patches/platinum_blitz_v1.0.xdelta
```

## Applying a Patch (for players)

Players need a verified vanilla ROM and xdelta3:
```bash
# Linux/WSL2
xdelta3 -d -s vanilla_platinum.nds platinum_blitz_v1.0.xdelta platinum_blitz.nds
```

On Windows: download the **xdelta UI** (GUI frontend) — apply the `.xdelta` patch to your vanilla ROM.

---

## Recommended Emulators

| Emulator | Platform | Notes |
|---|---|---|
| MelonDS | Windows/Mac/Linux | Best accuracy, recommended for testing |
| DeSmuME | Windows/Mac/Linux | Widely used, good compatibility |

---

## Troubleshooting

**`meson: command not found`**
Run `pip3 install --user meson` and ensure `~/.local/bin` is in PATH (`echo $PATH`).

**`arm-none-eabi-gcc: command not found`**
Ensure devkitARM is installed and `$DEVKITARM/bin` is in PATH. Run `source ~/.bashrc`.

**mwccarm license errors**
Run `mwccarm.exe` on Windows (not WSL2) once to initialize `license.dat`, then confirm `$LM_LICENSE_FILE` points to it.

**Build is very slow / permission errors**
You are building from `/mnt/c/...` — the Windows filesystem. Move the project to `~/Pokemon_Platinum_Blitz` (WSL2 native) and rebuild.

**SHA1 mismatch on clean build**
Use exactly the mwccarm and NitroSDK versions pinned in the PRET Discord. Different versions produce different output.
