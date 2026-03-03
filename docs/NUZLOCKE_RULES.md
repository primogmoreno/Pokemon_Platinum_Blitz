# Nuzlocke Rules — Pokemon Platinum Blitz

## Core Rules

1. **Permanent death** — If a Pokemon faints, it is dead. It cannot be used again for the rest of the run.
2. **Draft-only team** — You may only use Pokemon you drafted at auction. No catching wild Pokemon; no using mons outside your draft pool.
3. **No bag items in battle** — The Bag option has been removed from the battle action menu by the ROM. You cannot use potions, revives, or any item from your bag during a battle.
4. **Level cap** — Pokemon cannot gain experience past the level cap for the next boss. The ROM enforces this: EXP gain halts at the cap threshold. Pokemon gain **no EXP** from normal methods — instead, you receive **unlimited Rare Candies** at the start of the game to level up to the cap manually.
5. **No Reviving** — If a Pokemon faints, it cannot be revived by any means. Revives and Max Revives are non-functional (ROM-enforced).

---

## What Is Allowed

- Healing at Pokemon Centers
- Using held items in battle
- Visiting Pokemon Centers at any time
- Using TMs and move tutors
- Trading Pokemon between your boxes
- Using Rare Candies to level up to the current cap

## What Is Not Allowed

- Using bag items in battle (ROM-enforced)
- Using Pokemon not in your draft
- Reviving fainted Pokemon (ROM-enforced)
- Leveling Pokemon past the current cap (ROM-enforced)

---

## Level Cap Table

| Next Boss | Level Cap |
|---|---|
| Start | — |
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

---

## How the Cap Works

- Pokemon gain **no EXP** from normal battle methods
- You receive **unlimited Rare Candies** at game start — use them in your bag to level up to the cap
- EXP gain halts at exactly the cap level (`level_cap_threshold`)
- The cap rises after defeating each gym leader (badge flag triggers the update)
- A Pokemon at the cap level can still evolve if it meets the evolution condition — but it cannot gain further levels until the cap rises

---

## Story Gates Removed

The following mandatory story events have been bypassed so they do not block route access:

1. Eterna City — Team Galactic blockade
2. Lake Valor — Galactic bomb event
3. Lake Acuity — Galactic ambush event
4. Spear Pillar — Cyrus boss battle gate
5. Mt. Coronet — Galactic grunt blockades
6. Sendoff Spring — Post-Spear Pillar gate

> **Important:** The storyline should **not** exist in the game — cutscenes and dialogue related to Team Galactic story beats should not be present.

---

## Timing and Tiebreakers

- **Race start:** When the player confirms "New Game" (name entry complete)
- **Race end:** When Cynthia's Garchomp reaches 0 HP
- **Primary tiebreaker:** Real-world elapsed time from start to finish
- **Secondary tiebreaker:** In-game timer (accessible from the menu)

---

## Run Forfeit Conditions (DNF)

A run is considered a Did Not Finish (DNF) if:
- All six drafted Pokemon faint (party wipe)
- A player whites-out at any battle (all six Pokemon die)
- A player soft-resets without streaming evidence to prove the reset was unintentional

---

## Technical Notes

- Level cap enforced in `pokeplatinum/src/blitz_level_cap.c`
- Implementation uses an EXP hook that reads badge flag state to determine the active cap
- Edge cases handled: evolution at cap level, traded Pokemon, daycare Pokemon
- Cap is set to exactly `level_cap_threshold` (not `threshold - 1`)
