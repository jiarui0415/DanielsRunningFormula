---
name: running-training-planner
description: >
  Build a personalized running training plan using Jack Daniels' VDOT methodology.
  TRIGGER whenever the user wants to prepare for a race, create a training plan, get
  structured running coaching, or asks what workouts to do. Also trigger when the
  user says things like "I have a marathon in X weeks", "help me train for a 5K",
  "what should my training look like", or "I want to improve my running". Don't
  wait for them to say "training plan" explicitly — if they're trying to run better
  or race smarter, this skill applies. Reports weekly progress and adjusts upcoming
  training accordingly. DO NOT TRIGGER for casual single-answer questions like
  "what's a good pace for an easy run" or "convert 7:30/mile to km."
license: MIT
metadata:
  version: "1.0.0"
  author: "Charlie Zhang"
  methodology: "Jack Daniels' Running Formula (4th ed., 2022)"
---

# Running Training Planner

Generate personalized, evidence-based running training plans using Jack Daniels' VDOT methodology. Plans are organized by week with daily workouts, include strength training, and adapt based on weekly progress reports.

## Asset files (read when needed, not all at once)

- `assets/vdot-table.md` — VDOT lookup: race time → VDOT → training paces
- `assets/fvdot-decay.md` — VDOT decay by days off training (Table 9.1)
- `assets/workout-volume-caps.md` — per-session safety maximums (L/M/T/I/R)
- `assets/phase-compression.md` — Figure 10.2: phase weeks by total time available
- `assets/strength-circuit.md` — Jack's 10-station no-equipment circuit (ch.9)

---

## Step 1: Intake

Ask questions in two tiers. Don't ask everything at once — Tier 1 first, then Tier 2 once you understand the basic situation.

### Tier 1 — Ask these first (essential)

1. **Goal race:** What distance and when is the race?
2. **Recent race time:** What's your most recent race result? Distance, time, and roughly how long ago. (If no race, ask for a comfortable easy-run pace or offer a time trial.)
3. **Current weekly mileage:** About how many miles/km per week have you been running in the last month?
4. **Injury status:** Any current injury, persistent pain, or recent illness?
5. **Running consistency:** How many weeks have you been running consistently without a major break?

### Tier 2 — Ask before generating week-by-week workouts

6. **Goals:** What are your Goal A (dream), Goal B (solid), and Goal C (acceptable) finish times?
7. **Training days:** How many days per week can you train? (3–6+)
8. **Distance unit:** Miles, kilometers, or both?
9. **Week start:** Does your week start Sunday or Monday?
10. **Tune-up races:** Any races planned before the goal race? If so, approximate dates.
11. **Facility access:** Track available? Roads/trails only? Treadmill?
12. **Age:** (Ask only if they seem 40+, or if they volunteer it — affects VDOT adjustment from age 39+)

---

## Step 2: Calculate VDOT and configure the plan

### VDOT calculation

Read `assets/vdot-table.md`.

1. Look up the runner's recent race time → find the VDOT row
2. If the race was more than 5 days ago, read `assets/fvdot-decay.md` and apply decay:
   - Ask: "Did you do any aerobic cross-training (cycling, elliptical, pool running) during that time?" → use FVDOT-2 if yes, FVDOT-1 if no
   - Adjusted VDOT = VDOT × decay fraction
3. If multiple race times provided, use the highest adjusted VDOT
4. If no race time: offer a 1.5-mile or 3K time trial, or use their easy-run pace to estimate conservatively (add 2–3 VDOT units of caution)

**Age adjustment (from age 39+):** Jack's Table 5.6 shows VDOT adjustments for older runners. For now, note that a 58-year-old running 7:00/1600m is equivalent to a 38-year-old running 5:04/1600m. If the runner is 40+ and mentions they feel undertrained vs. their peers, acknowledge this context.

### Phase entry logic

Jack's phases progress E → R → I → T (non-negotiable order):
- Phase I: E runs + strides (from week 3+) + strength circuit
- Phase II: Adds R (repetition) workouts
- Phase III: Adds I (interval) workouts — hardest
- Phase IV: T (threshold) workouts + tune-up races

| Training history | Enter at |
|----------------|---------|
| Injury / illness / <4 weeks running | Phase I |
| 4–6 weeks E runs only | Phase II |
| 6+ weeks including R work | Phase III |
| 6+ weeks including R + I work | Phase IV |
| Raw beginner (no mileage base) | Novice path (see below) |

### Phase length allocation

Read `assets/phase-compression.md` for the Figure 10.2 table. Count weeks to race, look up the row, apply those phase lengths. Phase III is always dropped first when compressing.

**If entering mid-program** (e.g., runner already in Phase III equivalent): count remaining phases from entry point, not from Phase I.

### Mileage buildup rule

Week 1 target = current weekly mileage (don't jump up). Hold each mileage level for 3–4 weeks before increasing. Never increase more than ~30% above current weekly mileage in a single step. If the runner's current mileage is well below the plan tier, use Phase I to build gradually.

### Plan variant routing

Read `assets/workout-volume-caps.md` before prescribing any session.

| Distance | Scenario | Variant |
|----------|----------|---------|
| 5K / 10K | Any | 4-phase (ch.13): Phase II=R, III=I, IV=T |
| 15K / half marathon | Any | 2-week alternating cycle (ch.15) |
| Marathon | ≤55 mi/wk, ≥18 wks | 2Q 18-week (2 quality sessions/week) |
| Marathon | 56–85 mi/wk, ≥18 wks | 5-week cycle (alternating L/M week types) |
| Marathon | ≥86 mi/wk | Final 18-week high mileage |
| Marathon | 12–17 weeks | Compressed final 12-week |
| Marathon | Raw beginner | Novice 18-week (walk/run progression) |

**Half marathon vs 15K within ch.15:** Half marathon gets longer M runs and more T volume in the 2-week cycles.

---

## Step 3: Generate the plan (chunked output)

**Never generate the full plan at once.** Output 2–3 weeks per response. This keeps context manageable and lets the runner report progress before each new block.

### First response format

```
## Your Training Plan

**Goal race:** [distance] on [date] — [N] weeks away
**Current VDOT:** [XX] (adjusted from [YY] due to [N] days since race)
**Goal A VDOT target:** [XX] ([Goal A time])
**Mileage tier:** [XX] mi/wk
**Plan variant:** [name]
**Phase schedule:**
  Phase I: Weeks 1–N (Base / injury prevention)
  Phase II: Weeks N–N (Repetition focus)
  Phase III: Weeks N–N (Interval focus)
  Phase IV: Weeks N–race (Threshold + races)

---

### Your Training Paces (VDOT [XX])

| Type | Purpose | Per mile | Per km |
|------|---------|---------|--------|
| E (Easy) | All easy/long runs | X:XX–X:XX | X:XX–X:XX |
| M (Marathon) | Race-pace work | X:XX | X:XX |
| T (Threshold) | Tempo runs | X:XX | X:XX |
| I (Interval) | VO2max reps | X:XX/400m | X:XX/400m |
| R (Repetition) | Speed/economy | X:XX/400m | X:XX/400m |

Paces update when your VDOT updates (after races or significant training feedback).

---

## Weeks 1–3

[week blocks below]
```

### Week block format

```
## Week N of M — Phase [I/II/III/IV]: [Phase Name]
> Weekly target: XX miles | Long run cap: XX miles or 150 min

| Day | Session | Workout | Volume |
|-----|---------|---------|--------|
| Mon | E | Easy run | 40 min |
| Tue | Q1 | 2E + 4×(1200I w/ 3min jg) + 2E | 9 mi |
| Wed | E + ST | Easy run + 6×20sec strides (60sec walk rest) | 40 min |
| Thu | Q2 | 2E + 3×(1T w/ 1min rest) + 2E | 7 mi |
| Fri | E | Easy run | 35 min |
| Sat | E | Easy run | 30 min |
| Sun | L | Long easy run | 14 mi |

**Strength (2× this week, e.g. Mon + Sat after easy run):**
Full 3-round circuit from `assets/strength-circuit.md`

---

### Week N Check-in
Before I give you Weeks [N+1]–[N+3], let me know:
1. Did you complete all Q sessions? If not, which ones did you skip and why?
2. How did the training paces feel? (too easy / on target / too hard)
3. Any pain, unusual tightness, or fatigue worth mentioning?
4. Did you complete the long run? Actual distance or time?
5. Sleep quality this week: 1 (poor) – 5 (great)?
```

### Notation key

- **E** = Easy pace. Conversational. Use full pace range from VDOT table.
- **L** = Long run. Still at E pace — not "long and fast."
- **M** = Marathon pace. Controlled effort at goal marathon pace.
- **T** = Threshold pace. Comfortably hard — "10/10 effort you can hold for ~20–30 min."
- **I** = Interval pace. Hard. Individual bouts 3–5 min; full recovery between.
- **R** = Repetition pace. Fast. Individual bouts ≤2 min; full recovery between.
- **ST** = Strides. 20-second accelerations to near-R pace, 60-second walk recovery.
- **jg** = Jog recovery. Easy jogging between reps.
- **[OPTIONAL]** = Skip this session if fatigued; replace with E day.
- Format: `NE` = N miles at E pace; `N×(distPace w/ recoveryRest)` = N reps.

---

## Step 4: Process weekly check-ins

When the runner reports back, read their answers and decide:

**Raise VDOT by 1 unit** if:
- Race result maps to higher VDOT in vdot-table.md
- Multiple sessions felt very easy AND runner is confident in paces
- Rule: never raise more than 1 unit, and no more than once per 3 weeks

**Drop VDOT by 1 unit** if:
- Multiple Q sessions felt impossible or had to be cut short
- Runner reports unusual fatigue across multiple days
- Race result maps to lower VDOT

**Replace next Q session with E day** if:
- Fatigue is high but not systemic — might just be one bad week
- Runner skipped sessions due to work/life, not injury

**Pause plan and reassess from Phase I** if:
- New injury or illness
- Missed 7+ days of training → apply FVDOT decay from `assets/fvdot-decay.md`

When making adjustments, state them explicitly:
```
Based on your check-in, I'm updating your VDOT from 52 → 53 (paces felt consistently easy + time trial confirmed this). New paces are below. Here are Weeks 7–9:
```

---

## Step 5: Taper

### 5K / 10K
- Final 3–5 days: E runs only, no Q sessions
- Last Q session at least 4 days before race
- Optional race-week Q: 3 E days + short T workout (e.g., 3×1T w/ 1min rest) at day -5

### Half marathon / 15K–30K (ch.15 protocol)
Prerace week:
- Day -7: ⅔ of normal L run at E pace
- Day -6 to -5: E days
- Day -4: 3×(1 mile T w/ 2min rest)
- Day -3 to -2: E days
- Day -1: E day or rest
- Race day

### Marathon
3-week taper with gradual reduction. Final week (Week 1 of 18):
- Day 7: 90 min E
- Day 6: 60 min E
- Day 5: 10 min E + 4×(5 min T w/ 2 min rest) + 10 min E
- Day 4: 30–45 min E
- Day 3: 30 min E (may take off if travel required)
- Day 2: 30 min E
- Day 1: 30 min E
- Race day

---

## Strength training integration

Read `assets/strength-circuit.md` for the full circuit description.

**By phase:**
- Phase I–III: 2× per week after easy runs (full 3-round circuit)
- Phase IV: 1× per week after easy run
- Final 2 weeks (taper): drop strength entirely

Mention strength in the week block with specific day suggestions (e.g., "Monday and Saturday after easy runs"). Don't prescribe it on Q-session days.

---

## VDOT progression — built-in schedule

Bake VDOT check-in reminders into the plan at these points:
- After every tune-up race: "Look up your result in the VDOT table — if it maps to a higher value, we update your training paces"
- Every 3 weeks of consistent training: "Time to check in — have paces been feeling easy? We might bump VDOT by 1 unit"

State VDOT changes explicitly with before/after values.

---

## Novice path (raw beginners)

For runners with no mileage base or who have never raced:
- Start with walk/run intervals (e.g., 1 min run / 1 min walk × 15, building to continuous 30 min E)
- Phase I is extended to 8–10 weeks minimum
- No Q sessions until runner can complete 30 min continuous at E pace
- Use marathon novice plan (ch.16 Table 16.2) as the template
- Set conservative VDOT estimate (VDOT 30–35) until first race or time trial

---

## What makes this different from generic advice

Jack Daniels' system is built on three insights that generic training ignores:
1. **Single anchor point:** One recent race time sets *all* training paces. You don't guess easy pace, tempo pace, and interval pace separately — they all derive from VDOT.
2. **Volume caps protect you:** It's not just about adding hard workouts — each type has a per-session maximum based on your weekly mileage. Violating these caps is where injuries come from.
3. **Progression, not randomness:** Phases build in a specific order (E→R→I→T) because each phase develops a physiological system that the next phase relies on. Skipping ahead doesn't work.
