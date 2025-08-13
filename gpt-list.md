# Demon Bluff Player Assist Tool – Design Document

## Overview
A **player-assistance tool** for *Demon Bluff*, designed to run entirely in the browser via GitHub Pages.  
It helps organize claims, track deductions, and prevent common mistakes without playing or solving the game for you.  
No backend, no spoilers — everything is manually entered by the player.

---

## Principles
- **Assist, don’t play** – No hidden info automation or AI deduction.
- **Zero backend** – Runs entirely in browser, data saved locally.
- **Spoiler-safe** – No role text beyond public practical tells.
- **Fast at the table** – Compact UI, keyboard shortcuts, one-screen design.

---

## Layout

### 1. Deck Setup Validator
**You enter:**
- All Roles in the deck 
- Counts for **Disguisers** (Drunk, Doppelganger, Shaman)
- Counts for **Corruption sources** (Plague Doctor, Poisoner, Pooka)
- Counts for **Evils** (Demon, Minion, Puppet)

**Tool shows:**
- **Duplicate claims budget** (max legit double-claims)
- **Corruption capacity** (max likely corrupted villagers)
- Mode hint: **Solve-first** vs **Score-first** (Lilis-aware)

---

### 2. Village Board
- **Ring view** for adjacency/distance reasoning
- **Table view** toggle
- Seat cards:  
  `Player name | Claim | Revealed Role | Team | Disguised? | Corrupted? | Confirmed Good/Evil | Notes`
- **Distance tool** – For Enlightened/Hunter logic
- **Adjacency overlay** – Highlights pairs for Knitter, Poisoner/Pooka patterns

---

### 3. Claims Matrix & Conflict Flags
- Roles × Claimants grid
- Shows **duplicate budget remaining**
- Flags over-budget claims in amber
- After marking **Demon**, flags any same-role claimant as **Evil/Drunk candidate**

---

### 4. Statements Board
- Add short statements, mark as **True / False / Unknown**
- Auto-group:
  - Identical truths → likely good cluster
  - Identical falsehoods → likely evil/corrupted
- Warn on unlikely “3 identical truths”
- Wretch logic: any detector who saw Wretch as evil → mark confirmed good

---

### 5. Role Assistants
Small calculators for fast, safe deductions:

- **Alchemist helper** – Compare cured count to corruption capacity; auto-mark safe 0’s
- **Baker tracker** – Track original/turned Bakers; turned = confirmed good
- **Fortune Teller picker** – Pick 2 seats, log results; self-confirm tips
- **Judge/Jester flows** – Lie check and 3-pick evil counts
- **Medium/Oracle/Dreamer cross-checks** – Special-case triggers
- **Plague Doctor tester** – Self-check, dead corrupted check, evil pings
- **Pooka/Poisoner visualizer** – Highlights neighbors; pattern matching
- **Knitter pairs overlay**
- **Slayer safety** – Puppet exception logic
- **Knight corruption warning** – 9-damage alert 
- **Bombardier interlock** – “Are you sure?” modal before execution

---

### 6. Locks & Timers
- **Lilis counter** – Tracks reveals since last kill, warns before reset
- **Witch lock** – Blocks last-card reveal while Witch alive

---

### 7. Session & Sharing
- Auto-save to localStorage
- Export/Import JSON
- Shareable link (state encoded or copy-paste)
- New round templates with deck presets

---

## Data Model (No Code Yet)
```yaml
Deck:
  roles: [string]
  hasShaman: bool
  hasDoppel: bool
  hasLilis: bool
  hasWitch: bool
  corruption:
    plagueDoctor: bool
    poisoner: bool
    pooka: bool
  derived:
    duplicateBudget: number
    corruptionCapacity: number

Seat:
  id: string
  index: number
  name: string
  claim?: string
  revealedRole?: string
  team: 'good' | 'evil' | 'outcast' | 'unknown'
  disguised?: bool
  corrupted?: bool
  confirmedGood?: bool
  confirmedEvil?: bool
  notes?: string

Statement:
  id: string
  text: string
  seatsInvolved: [string]
  truth: 'unknown' | 'true' | 'false'
  sourceRole?: string

Counters:
  revealsSinceLilisKill: number
  witchAlive: bool
  perDayUsesSpent: { role: number }
