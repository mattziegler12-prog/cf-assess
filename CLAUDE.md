# cf-assess

## Purpose
Single-file HTML CrossFit performance assessment tool for CrossFit Vic City — up to 10 athlete profiles with PIN auth, benchmark logging with Garmin HR data, injury/limitation flags that modify workouts, and a self-assessment questionnaire. No backend, no build step, all data in localStorage.

## Stack
- **Language/runtime:** Vanilla JS + HTML (no framework, no npm, no build step)
- **Storage:** localStorage (key: `cfassess_users`)
- **Fonts:** Google Fonts CDN — Barlow Condensed, Barlow, DM Mono
- **Deployment target:** GitHub Pages (`~/Developer/personal/cf-assess`)
- **Units:** Lbs throughout — intentional and fixed, do not add a kg toggle

## Folder Structure
```
cf-assess/
├── CLAUDE.md               ← this file
├── index.html              ← rename of cf-assess-v3.html
├── README.md
└── assets/                 ← future: if CSS/JS ever split out
```

## Run Commands
- **Dev:** Open `index.html` directly in a browser — no server needed
- **Build:** None — the file is the artifact

## Current Status
**Working:**
- Login screen: up to 10 profiles, 4-digit PIN auth, color-coded avatars, last-logged date, new-user → injury intake flow
- Benchmark Tab: 9 benchmarks (Pull-up Test, Fran, Double-Under, Grace, OHS 3RM, Snatch 1RM, 2K Row, Cindy, 5K Run) with Rx/Scaled/Modified toggle, A/B/C/D grading, bodyweight-ratio grading for OHS/Snatch, Garmin HR fields, grade-specific accessory drawers, injury modification notes
- Self-Assessment Tab: 18 questions across 3 domains, 0–3 scoring, domain bar charts, weakness/strength cards with accessory work, results persist per user
- Profile Tab: bodyweight field, 6 injury flags (overhead, lower_back, knee, shoulder, wrist, cardio), summary stats, per-user data clear

**Not yet done (first session):**
- File still named `cf-assess-v3.html` — needs rename to `index.html`
- Git not initialized, not yet on GitHub, Pages not enabled

## Next Task
Rename `cf-assess-v3.html` → `index.html`, initialize git, push to the `cf-assess` repo on GitHub, and enable GitHub Pages.

## Known Issues / Constraints
- **Max 10 users** hard-capped in `createUser()` — do not raise without reason
- **Lbs only** — no kg, no toggle; all grading thresholds are in lbs
- **Only most-recent result stored per benchmark** — history log is the top-priority missing feature
- **Fonts require CDN** — offline use falls back to system fonts
- If user count ever exceeds 10 or cross-device sync is needed, migrate to FastAPI + SQLite (same stack as HydroCalc)

### Architecture decisions (do not change without reason)
| Decision | Rationale |
|---|---|
| Single HTML file | No build tooling, no hosting complexity — coaches can open it from anywhere |
| localStorage only | No backend needed at ≤10 users; all data stays on device |
| Lbs only | All users are at a Canadian gym that programs in lbs |
| Max 10 users | Hard cap enforced in createUser(); reasonable for one gym class |
| PIN auth | Fast on mobile, low friction, not security-critical |
| No framework | Vanilla JS + HTML — no npm, no build step, no dependencies |
| Fonts via Google Fonts CDN | Barlow Condensed + Barlow + DM Mono — consistent with design |

### Upcoming features (prioritized)
1. **Benchmark history log** — store array of results with dates; show sparkline/delta vs previous on card
2. **GitHub Pages deployment** — public URL so athletes can access from gym phones without file sharing
3. **Combined priority list** — fourth tab merging gaps from benchmarks + self-assessment into ranked weekly accessory prescription
4. Printable/shareable summary (PDF-style, for coach review)
5. Admin view (PIN-protected, read-only, all users side by side)

### Benchmark grading thresholds (Rx, lbs)
| Benchmark | A | B | C | D |
|---|---|---|---|---|
| Pull-up Test (3-min AMRAP) | 30+ reps | 20–29 | 10–19 | <10 |
| Fran | <3:30 | 3:30–6:00 | 6:01–10:00 | >10:00 |
| Double-Under Test (2-min) | 200+ reps | 120–199 | 60–119 | <60 |
| Grace (135/95 lbs) | <2:00 | 2:00–4:00 | 4:01–7:00 | >7:00 |
| OHS 3RM | ≥1.0× BW | 0.7–1.0× BW | 0.4–0.7× BW | <0.4× BW |
| Snatch 1RM | ≥0.9× BW | 0.65–0.9× BW | 0.4–0.65× BW | <0.4× BW |
| 2K Row | <7:00 | 7:00–8:00 | 8:01–9:30 | >9:30 |
| Cindy (20-min AMRAP) | 25+ rounds | 20–24 | 14–19 | <14 |
| 5K Run | <21:00 | 21:00–25:00 | 25:01–30:00 | >30:00 |

Scaled thresholds are defined in the BENCHMARKS array in `index.html`.

### Injury flag → benchmark restriction map
| Flag ID | Restricts (benchmark IDs) |
|---|---|
| overhead | ohs3rm, snatch1rm, grace |
| lower_back | (none hard-restricted, notes only) |
| knee | ohs3rm, snatch1rm |
| shoulder | pull3min, fran |
| wrist | grace, snatch1rm, ohs3rm |
| cardio | (none hard-restricted, notes only) |

Each benchmark has an `injuries{}` map with per-flag modification guidance strings that surface in the accessory drawer.

### localStorage data model
```json
{
  "Matt": {
    "name": "Matt",
    "pin": "1234",
    "injuries": ["wrist", "overhead"],
    "bodyweight": 185,
    "benchResults": {
      "fran": {
        "grade": "B", "rawInput": "4:22", "rawValue": 262,
        "displayVal": "4:22", "date": "2026-05-04", "scaled": "rx",
        "hrMax": "182", "hrAvg": "171", "hrPct": "96",
        "notes": "Went out too hot on 21s", "extra": null, "bw": 0
      }
    },
    "assessAnswers": { "pullup": 2, "muscle_up": 1 },
    "assessDone": true,
    "createdAt": "2026-05-04T00:00:00.000Z"
  }
}
```

## Session Protocol
**Opening:** "Read CLAUDE.md and confirm you understand the project before doing anything."
**Closing:** "Update CLAUDE.md — keep the same sections, just update Current Status and Next Task."
