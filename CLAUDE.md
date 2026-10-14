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
- **Taste**: not scored. A word or two in `notes` if anything stands out
  (sour, bitter, watery, OK).
- Optional: time to first drops, crema notes.

### Logged but not controlled

- Temperature surf: optional. Silvia's boiler swings ~10 °C. If recorded,
  log seconds since the heating light went off when brew was pressed.

### Explicitly NOT tracked

Do not suggest tracking these. The user has decided against them.

- Roast date / days since roast.
- Hopper fill level.
- Holding grinder run time constant (it is the dose lever, see above).
- Numeric taste scores (sour/bitter, body, overall). Taste goes in `notes` as
  a word, if at all.

## Procedure

1. Warm the machine with the portafilter locked in.
2. Set grind setting. Set grinder run time. Grind into the portafilter, weigh,
   record dose. If dose is off target, change run time and note it.
3. Distribute and tamp the same way every time.
4. Cup on scale, tare, start brew and a timer together.
5. Stop at target yield. Record yield and shot time.
6. Taste. Note sour / bitter / watery / OK if it is worth saying.
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
| notes | free text, including any taste remark |

Leave a cell blank if not measured. Ratio and flow rate are derived, not
stored.

## Current state

- 2026-10-08: grind setting 3.1 (collar ring runs 0.0 to 10.0). At 3.1,
  9.00 s of grinder run time gave 22 g dose, ~2.4 g/s throughput. 22 g is high
  for a Silvia double basket.
- 2026-10-09: at 3.1, 7.40 s gave 16 g. Dose is not proportional to run time
  (motor spin-up / retention offset): the two points fit ~3.75 g per extra
  second, so 18 g ≈ 7.9 s.
- 2026-10-12: at 3.1, 7.90 s gave 19 g. First full shot: 36 g out (1:1.9),
  tasted OK. Shot time not recorded.
- 2026-10-14: at 3.1, 7.90 s gave 20 g and 7.70 s gave 17 g (scale is 1 g
  resolution, so expect ±0.5 g noise). Both shots watery: 36 g out in ~10 s,
  i.e. ~3.6 g/s. That is 2 to 3 times too fast. Run time now set to 7.85 s.
- Plan: the grind is far too coarse for this basket. Stop adjusting run time
  and go finer. Move the collar from 3.1 to about 2.5, then re-check dose on
  the scale (finer grinds slower, so run time will need to go up). Target is
  36 g in 25 to 32 s. If still under 20 s, go finer again in steps of ~0.3.

## How Claude should work on this repo

- When the user reports a shot, append a row to `shots.csv` and, if asked,
  suggest the next single change based on the rules above.
- Change one variable at a time between shots unless the user says otherwise.
- Respect the "explicitly NOT tracked" list. Do not relitigate it.
- Keep everything in plain text files in this repo so it survives a machine
  change.
- After any change to a file in this repo, commit and push straight away.
  Do not ask first.
