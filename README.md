# Daniels Running Formula — Claude Code Skill

A Claude Code skill that generates personalized running training plans using Jack Daniels' VDOT methodology. Plans are organized by week with daily workouts, include strength training, and adapt based on weekly progress reports.

## What it does

- Asks a focused set of intake questions (goal race, recent race time, weekly mileage, injury status, etc.)
- Calculates your VDOT from race performance, applies decay for time off training
- Selects the right Jack Daniels plan variant for your distance and mileage tier
- Outputs 2–3 weeks at a time with daily workouts at scientifically correct paces
- Includes strength training (Jack's 10-station no-equipment circuit)
- Prompts weekly check-ins and adjusts upcoming training based on your progress

## Methodology

Built on Jack Daniels' *Running Formula* (4th ed., 2022):
- **VDOT system** — one race time anchors all training paces (E, M, T, I, R)
- **4-phase structure** — Base → Repetition → Interval → Threshold (E→R→I→T)
- **Volume caps** — per-session safety limits enforced for every workout type
- **Phase compression** — correct phase allocation when fewer than 24 weeks available

## Installation

### Requirements

- [Claude Code](https://claude.ai/code) CLI installed
- Plugins support enabled

### Steps

1. Clone this repository:

```bash
git clone git@github.com:jiarui0415/DanielsRunningFormula.git
```

2. Copy the skill into your Claude Code skills directory:

```bash
cp -r DanielsRunningFormula/skill ~/.claude/skills/running-training-planner
```

3. Verify the skill is available. Start a Claude Code session and run:

```
/find-skills running
```

You should see `running-training-planner` in the list.

### Usage

Start a Claude Code session and say something like:

- "I have a marathon in 18 weeks, help me train for it"
- "Create a training plan for my 5K race in 8 weeks"
- "I just got cleared to run after 10 weeks off, my goal is a half marathon"

The skill will ask intake questions, calculate your VDOT, and generate a week-by-week plan.

### Weekly check-ins

After each 2–3 week block, report back:
1. Did you complete all quality sessions?
2. How did paces feel?
3. Any pain or unusual fatigue?
4. Long run completed?
5. Sleep quality (1–5)?

The skill adjusts upcoming training based on your answers.

## Skill structure

```
skill/
├── SKILL.md                    # Main skill instructions
├── CREDITS.md                  # Attribution
└── assets/
    ├── vdot-table.md           # VDOT → training paces lookup
    ├── fvdot-decay.md          # VDOT decay by days off training
    ├── workout-volume-caps.md  # Per-session safety maximums
    ├── phase-compression.md   # Phase weeks by total time available
    └── strength-circuit.md    # 10-station no-equipment circuit
```

## Credits

Based on Jack Daniels' *Running Formula* (4th ed., 2022), Human Kinetics. VDOT tables originally developed by Jack Daniels and Jimmy Gilbert.
