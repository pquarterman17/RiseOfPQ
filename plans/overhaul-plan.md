# Rise of PQ — Epic Pacing Overhaul

Gameplay/balance overhaul for Rise of Nations: Extended Edition targeting slower, grander single-player games: longer ages, higher population ceilings, more expensive late game. Also covers two quality-of-life workstreams outside the mod itself: borderless-windowed display and graphics quality.

**Status:** Active
**Created:** 2026-06-07
**Updated:** 2026-06-07

> Items 1–3, 8, 11, and 12 are implemented and deployed but **await in-game verification** — their checkboxes stay open until a test game confirms them.

---

## Context

### How the pieces fit together
The mod payload lives in `mod/Rise of PQ/data/` and deploys to `<game>\mods\Rise of PQ\` via `tools/deploy.ps1`. Each file is a whole-file override of the same-named file in `<game>\Data\`. Pristine baselines live in `vanilla/`. Display settings (W2) are NOT mod files — they live in `%APPDATA%\Microsoft Games\Rise of Nations\rise2.ini` and are managed by `tools/borderless.ps1`. Graphics constants (W3) live in `<game>\graphics.txt` (game root, also not moddable via the mods folder).

### Data / control flow
```
vanilla/<file>.xml ──copy──> mod/Rise of PQ/data/<file>.xml ──edit──> deploy.ps1
                                                                        │
<game>\mods\Rise of PQ\ <──────────────────────────────────────────────┘
        │  (enable in-game: Main Menu → Mods)
        ▼
game loads mod file INSTEAD of Data\<file>.xml
```

### Dependency map
- W1 items 1–4 are independent of each other (parallelizable)
- Item 5 (unit cost ramps) should follow item 1 (age pacing) — tuning order matters
- W2 (item 8) and W3 (items 9–10) are fully independent of W1
- Item 7 (AI sanity pass) must come after items 1–5 land

---

## Cross-cutting priorities

| # | Item | Workstream | Why first |
|---|------|------------|-----------|
| 8 | Borderless windowed | W2 — Display | One ini edit; immediate QoL while developing the mod |
| 1 | Age pacing | W1 — Pacing | The core of "epic pacing"; everything else tunes around it |
| 2 | Population caps | W1 — Pacing | Small, high-impact, independent |

---

## W1 — Epic Pacing (the mod)

### Tier 1 — High Impact

1. **Age pacing** — scale the 7 Age techs' `COST` and `JOB_TIME` in `techrules.xml` (~2× cost, ~2× research time as starting point)
   - [x] Copy `techrules.xml` from vanilla into mod
   - [x] Scale Age tech costs and JOB_TIME (all 7 ages, 2× both)
   - [ ] In-game verification: Classical Age arrives noticeably later in a test game

2. **Population caps** — raise `POP_CAP` table entries and `MAX_POP_LIMIT` in `rules.xml` (setup choices 50–400, hard cap 999)
   - [x] Copy `rules.xml` from vanilla into mod
   - [x] Raise POP_CAP entries + MAX_POP_LIMIT
   - [ ] Verify setup screen shows new choices and units build past 200

3. **Mod skeleton + deploy pipeline** — `info.xml`, `tools/deploy.ps1`, first successful in-game load of the mod
   - [x] info.xml with name/description
   - [x] deploy.ps1 copies mod → game mods folder
   - [ ] Mod appears and enables in Main Menu → Mods

### Tier 2 — Medium Impact

4. **Library tech pacing** — scale non-Age Library techs (Military/Civic/Commerce/Science lines) in `techrules.xml`/`balance.xml` so the tech tree stretches with the ages

11. **Resource production boost** — double `COMMERCE_CAP` per-age income ceilings (70–500 → 140–1000) and `CITY_GATHER` base city income (10/10 → 20/20) in `rules.xml`
   - [x] COMMERCE_CAP doubled
   - [x] CITY_GATHER doubled
   - [ ] In-game verification: income caps show doubled values in economy tooltip

12. **Auto-train standalone script** — `scripts/Auto_Train/auto_train.bhs`: periodic `train_unit` for citizens (idle-gated), scholars, optional military lines; deployed to `scenario\Scripts\`
   - [x] Script written using shipped Auto_Pause/No_Nukes as template
   - [x] deploy.ps1 extended to install standalone scripts
   - [ ] In-game verification: select script in game setup, confirm citizens auto-queue and messages print
   - [ ] Tune defaults (interval, batch) after first playtest

5. **Late-game unit economy** — raise `COST`/`SUPPORT` ramps for Industrial+ units in `unitrules.xml` so late armies are investments, not spam

6. **Building pacing** — longer build times for wonders and late-game buildings in `buildingrules.xml`

### Tier 3 — Nice-to-Have

7. **AI sanity pass** — long test games vs AI at each difficulty; confirm AI still ages up and fields armies under the new economy (tune if it stalls)

---

## W2 — Borderless Windowed Display

### Tier 1 — High Impact

8. **Borderless windowed mode** — `rise2.ini`: `Fullscreen=0` with `Windowed Width/Height` = desktop resolution; keep `IgnoreMinimizeOnTabOut=1`
   - [x] tools/borderless.ps1 toggle script with backup (applied 2026-06-07, backup at rise2.ini.bak)
   - [ ] Verify: game fills screen, second monitor/window usable without minimize
   - [ ] If a title bar appears at full res, investigate EE windowed-borderless behavior / fallback options

---

## W3 — Graphics Quality

### Tier 2 — Medium Impact

9. **In-engine quality settings audit** — max out `Anti-Aliasing`, texture/detail options in rise2.ini and in-game options; document what each does

10. **Terrain render constants** — experiment with `graphics.txt` draw-distance/detail constants; keep a vanilla backup (already in `vanilla/graphics.txt`)

---

## Completed

(nothing yet)
