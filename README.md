# Pokemon Platinum Blitz

A ROM hack of Pokemon Platinum designed for **draft-auction nuzlocke races**, modeled after Pokemon Emerald Blitz. Players draft a team of Pokemon at auction before the run, then race through the Sinnoh gym circuit under nuzlocke rules.

---

## Credits
Frankly Nathan for inspiring this idea to be done in Pokemon Platinum
https://eeveeexpo.com/resources/1101/ - For compling pixel sprites available for ROM Hack use

## Features (v1.0)

### Enhanced Boss Battles
Every gym leader, rival encounter, Elite Four member, and Champion Cynthia has been redesigned with:
- Full 6-Pokemon teams (gym leaders upgraded from 3)
- Competitive movesets with full TM/tutor coverage
- 31 IVs in all stats on every trainer Pokemon
- Optimized held items (Life Orb, Choice Scarf, Leftovers, etc.)
- Maximum AI difficulty flags

### Nuzlocke Enforcement
- **Items disabled in battle** — the Bag option is removed from the battle action menu
- **Level cap system** — Pokemon cannot level past the highest-level Pokemon on the next boss's team

| Next Boss | Level Cap |
|---|---|
| Roark | 14 |
| Gardenia | 22 |
| Maylene | 28 |
| Crasher Wake | 35 |
| Fantina | 40 |
| Byron | 45 |
| Candice | 52 |
| Volkner | 58 |
| Elite Four (entry) | 62 |
| Cynthia | 68 |

### Streamlined Navigation
- **HM-free overworld** — Surf, Cut, Strength, Rock Smash, Waterfall, and Rock Climb work without a party Pokemon that knows the move
- **Shortened intro** — The Lake Verity opening sequence is condensed so players reach Twinleaf Town quickly
- **Story gates removed** — Team Galactic mandatory events that block route access are bypassed (Eterna, Lake trio events, Spear Pillar)

---

## Rules

### Before the Race
1. All participants agree on the **draft pool** (list of eligible Pokemon) — see `docs/DRAFT_POOL.md`
2. Run an auction draft: participants bid a starting budget to claim Pokemon from the pool
3. Each participant races through Platinum using only their drafted Pokemon

### During the Race
- **Nuzlocke rules apply:** If a Pokemon faints, it is permanently dead — it may not be used again
- **No bag items in battle** (enforced by the ROM)
- **No overleveling** — respect the level cap for the next boss (enforced by the ROM)
- **No catching** — you only use your drafted team
- **You may** use items outside of battle (healing at Pokemon Centers is allowed)

### Finishing
- Race ends when Cynthia is defeated
- If all drafted Pokemon faint, the run is over (did not finish)

---

## Installation

See [INSTALL.md](INSTALL.md) for full build and patch application instructions.

**For players applying a pre-built patch:**
1. Obtain a verified Pokemon Platinum USA Rev 1 ROM
2. Apply the `.xdelta` patch from the `patches/` directory using xdelta3
3. Load the resulting `.nds` file in MelonDS or DeSmuME

---

## Project Structure

```
Pokemon_Platinum_Blitz/
├── pokeplatinum/       # Forked pret/pokeplatinum decompilation (submodule)
├── docs/
│   ├── BOSS_BATTLES.md     # All gym leader / E4 / Cynthia team designs
│   ├── DRAFT_POOL.md       # Curated list of draftable Pokemon (future)
│   └── NUZLOCKE_RULES.md   # Level cap table and rule clarifications
├── patches/            # Distributable .xdelta patches
├── INSTALL.md          # Build environment setup
└── README.md           # This file
```

---

## Credits

- **Base decompilation:** [pret/pokeplatinum](https://github.com/pret/pokeplatinum) and the pret community
- **Inspired by:** Pokemon Emerald Blitz by the Rom Hacking Hideout community
- **Build system reference:** [plat-qol](https://github.com/lhearachel/plat-qol) by lhearachel
