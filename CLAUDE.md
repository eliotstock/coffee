# Coffee dialling-in project

Goal: stop getting watery, inconsistent espresso. We vary input variables and
score the result, logging every shot in `shots.csv`. This file is the full
context needed to pick the project up on a new machine.

## Equipment

- Grinder: Mazzer, electronic timed dosing (MENU / + / - buttons, run time in
  seconds shown on the display, portafilter fork). Stepless grind collar with a
  numbered ring from 0.0 to 10.0; record the number on the ring as the grind
  setting.
- Machine: Rancilio Silvia. Single boiler, no PID, no pressure gauge, no
  volumetric stop. The brew switch is manual: the shot runs until it is
  switched off.
- Scale with 0.1 g resolution for dose and yield.
- Coffee is always black. Milk is out of scope.

## Current bean

Supreme (brand), Supreme (beans), whole beans.

## Variables

### Independent: things we deliberately set

1. **Grind setting** (number on the Mazzer collar). The main lever for shot
   time and extraction.
2. **Grinder run time** (seconds, set on the Mazzer timer). This is the lever
   used to hit a target dose at a given grind setting. It is varied freely and
   is NOT held constant. Throughput changes with grind setting (finer grinds
   slower), so after any grind change the run time needs re-checking against
   the scale.
3. **Yield** (grams in the cup). The shot is stopped on weight, with the cup
   on the scale, not on time and not by eye. This is the fix for the watery
   problem: on a Silvia nothing stops the shot for you.

### Derived from the above

- **Dose** (grams of grounds in the portafilter) = f(grind setting, run time).
  We aim for a target dose and adjust run time to hit it.
- **Brew ratio** = yield / dose. Starting target 1:2 (e.g. 18 g in, 36 g out).

### Measured: how we judge the shot

- **Shot time** (seconds from brew switch on to off at target yield).
- **Flow rate** = yield / shot time (g/s).
- **Taste**, scored per shot:
  - sour_bitter: 1 (sour) to 5 (bitter), 3 is balanced
  - body: 1 (thin, watery) to 5 (syrupy)
  - overall: 1 to 10
- Optional: time to first drops, crema notes.

### Logged but not controlled

- Temperature surf: optional. Silvia's boiler swings ~10 °C. If recorded,
  log seconds since the heating light went off when brew was pressed.

### Explicitly NOT tracked

Do not suggest tracking these. The user has decided against them.

- Roast date / days since roast.
- Hopper fill level.
- Holding grinder run time constant (it is the dose lever, see above).

## Procedure

1. Warm the machine with the portafilter locked in.
2. Set grind setting. Set grinder run time. Grind into the portafilter, weigh,
   record dose. If dose is off target, change run time and note it.
3. Distribute and tamp the same way every time.
4. Cup on scale, tare, start brew and a timer together.
5. Stop at target yield. Record yield and shot time.
6. Taste and score.
7. Append a row to `shots.csv`.

Dialling-in rules of thumb (grind is the only thing changed between shots
until time is in the window):

- Shot time under ~20 s: grind finer.
- Shot time over ~35 s: grind coarser.
- In the 25 to 32 s window: leave grind alone and start adjusting on taste.
  Sour → finer. Bitter → coarser.
- Only once that is stable, experiment with ratio (1:1.5, 1:2, 1:2.5).

## Log: shots.csv

One row per grind or shot. Columns:

| column | meaning |
| --- | --- |
| date | YYYY-MM-DD |
| time | HH:MM local |
| bean | brand / beans |
| grind_setting | number on the Mazzer collar |
| grind_s | grinder run time, seconds |
| dose_g | grounds in portafilter, grams |
| yield_g | coffee in cup, grams |
| shot_s | brew time, seconds |
| first_drops_s | optional |
| surf_s | optional, seconds since heating light went off |
| sour_bitter | 1 to 5 |
| body | 1 to 5 |
| overall | 1 to 10 |
| notes | free text |

Leave a cell blank if not measured. Ratio and flow rate are derived, not
stored.

## Current state

- 2026-10-08: grind setting 3.1 (collar ring runs 0.0 to 10.0). At 3.1,
  9.00 s of grinder run time gave 22 g dose, ~2.4 g/s throughput. 22 g is high
  for a Silvia double basket.
- Plan: hold grind at 3.1 and reduce run time until dose is 18 g. Starting
  guess ~7.4 s. Re-check on the scale each time.

## How Claude should work on this repo

- When the user reports a shot, append a row to `shots.csv` and, if asked,
  suggest the next single change based on the rules above.
- Change one variable at a time between shots unless the user says otherwise.
- Respect the "explicitly NOT tracked" list. Do not relitigate it.
- Keep everything in plain text files in this repo so it survives a machine
  change.
