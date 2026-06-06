# TSB Editor

A browser-based ROM editor for **Tecmo Super Bowl** (NES, 1991).

Built by reverse engineering the NES ROM binary directly — player data offsets, name encoding, ability structure, and slot sizing were all derived from scratch by cross-referencing the raw bytes against known player stats and community disassembly documentation.

---

## Usage

1. Download `TSB_Editor.html`
2. Open it in **Chrome** or **Edge**
3. Drag and drop your ROM onto the load screen, or click to browse
4. Select a team from the sidebar
5. Edit players inline
6. Hit **Save Team** to commit changes to the in-memory ROM
7. Hit **Export ROM** to download the finished `.nes` file

No installation. No server. Everything runs locally in your browser.

---

## Supported ROM

| File | Notes |
|------|-------|
| `Tecmo Super Bowl (USA).nes` | Standard iNES format, verified working |

The editor validates the iNES magic bytes on load and will reject non-NES files.

---

## Features

### Editing
- All 28 NFL teams across both conferences and six divisions
- Full inline editing -- no separate edit mode required
- Players grouped by Offense, O-Line, and Defense

### Player fields
| Field | Description |
|-------|-------------|
| First name | Full lowercase first name as stored in ROM |
| Last name | Up to 12 characters uppercase |
| Jersey number | 0–99, stored as BCD |
| RP | Rushing Power (all positions) |
| RS | Running Speed (all positions) |
| MS | Maximum Speed (all positions) |
| HP | Hitting Power (all positions) |
| PS / PC / PA / APB | QB-specific: Pass Speed, Pass Control, Pass Accuracy, Avoid Pass Block |
| BC / REC | Skill players: Ball Control, Receptions |
| KA / AKB | Kicker/Punter: Kick Ability, Avoid Kick Block |
| PI | Defensive players: Pass Interceptions |
| FACE / BODY | Appearance values (raw) |

All stats snap to the TSB scale: `6, 13, 19, 25, 31, 38, 44, 50, 56, 63, 69, 75, 81, 88, 94, 100`

### Tools
- **Undo/Redo** — up to 30 steps per session
- **Copy/Paste** — copy any player onto any slot, including across teams
- **Bulk stat adjust** — set a stat across an entire position group at once
- **Player search** — search by name across all 28 teams
- **Compare view** — side-by-side stat comparison of any two teams
- **Reorder players** — swap players within a position group
- **Collapsible sidebar** — hide team list for more editing space
- **Export CSV** — export the current team roster to a spreadsheet

### File handling
- **Save Team** — writes edits to the in-memory ROM buffer
- **Export ROM** — downloads the finished `.nes` file ready for an emulator
- **Revert** — rolls back unsaved changes on the current team
- **Eject** — unloads the ROM and returns to the load screen, with unsaved-changes prompt

---

## ROM Data Structure

Player ability data begins at file offset `0x3010`, with 117 bytes per team (28 teams in order).

Each team block contains 30 player slots in this order:

| Slots | Positions | Nibbles each |
|-------|-----------|-------------|
| 0–1 | QB1, QB2 | 10 |
| 2–11 | RB1–4, WR1–4, TE1–2 | 8 |
| 12–13 | K, P | 8 |
| 14–18 | C, LG, RG, LT, RT | 6 |
| 19–29 | RE, NT, LE, ROLB, RILB, LILB, LOLB, RCB, LCB, FS, SS | 8 |

Within each slot, nibble layout is:

```
[0] RP  [1] RS  [2] MS  [3] HP  [4] FACE  [5] BODY  [6–N] position-specific
```

Nibble values `0x0–0xF` map to: `6, 13, 19, 25, 31, 38, 44, 50, 56, 63, 69, 75, 81, 88, 94, 100`

Player names are stored as sequential variable-length records in PRG ROM. Each record contains a BCD-encoded jersey number followed by a lowercase first name and uppercase last name, with no fixed-length padding. The editor calculates each slot's byte budget from the pointer table and pads writes with spaces to prevent name bleed.

Team order matches the ability data index:

```
0  BUF BILLS       7  HOU OILERS      14 WAS REDSKINS   21 GB PACKERS
1  IND COLTS       8  PIT STEELERS    15 NYG GIANTS      22 MIN VIKINGS
2  MIA DOLPHINS    9  DEN BRONCOS     16 PHI EAGLES      23 TB BUCCANEERS
3  NE PATRIOTS    10  KC CHIEFS       17 PHX CARDINALS   24 SF 49ERS
4  NYJ JETS       11  LAR RAIDERS     18 DAL COWBOYS     25 LA RAMS
5  CIN BENGALS    12  SD CHARGERS     19 CHI BEARS       26 NO SAINTS
6  CLE BROWNS     13  SEA SEAHAWKS    20 DET LIONS       27 ATL FALCONS
```

---

## What the ratings mean

**All positions**
- **RP** — Rushing Power: blocking and tackle-breaking strength
- **RS** — Running Speed: acceleration
- **MS** — Maximum Speed: top-end speed
- **HP** — Hitting Power: tackle and block effectiveness

**QBs**
- **PS** — Pass Speed: velocity of thrown ball
- **PC** — Pass Control: determines completion probability (most important QB stat)
- **PA** — Pass Accuracy: targeting accuracy (note: has no measurable gameplay effect per community research)
- **APB** — Avoid Pass Block: ability to escape the pass rush

**Skill players (RB, WR, TE)**
- **BC** — Ball Control: ball security and yards after contact
- **REC** — Receptions: catching ability

**K / P**
- **KA** — Kick Ability: distance and accuracy
- **AKB** — Avoid Kick Block: protection from blocks

**Defensive players**
- **PI** — Pass Interceptions: coverage and pick ability
- **QK** — Quickness: has no measurable gameplay effect per community research

---

## Disclaimer

This tool is a fan project for personal use. Tecmo Super Bowl is the property of Tecmo. No ROM files are distributed with this project. Use only with a legally obtained ROM.

---

## License

MIT License — see `LICENSE` for details.

The MIT licence covers the editor code only and does not grant any rights to the game or its assets.
