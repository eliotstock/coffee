# Coffee dialling-in project

Goal: stop getting watery, inconsistent espresso. We vary input variables and
score the result, logging every shot in `shots.csv`. This file is the full
context needed to pick the project up on a new machine.

## Equipment

- Grinder: Mazzer, electronic timed dosing (MENU / + / - buttons, run time in
  seconds shown on the display, portafilter fork). Stepless grind collar with a
  numbered ring from 0.0 to 10.0; record the number on the ring as the grind
  setting. LOWER number = FINER grind. The circles engraved on the collar
  grow larger towards the higher numbers, which is the intended cue. Burrs
  touch (chirp) at 1.2; that is the true zero. Espresso range is expected
  roughly 1.7 to 3.0. (Earlier notes assumed the opposite direction; that
  mistake is why the 2026-10-14 to 10-19 grind changes did nothing and why
  the grinder jammed.)
- Machine: Rancilio Silvia. Single boiler, no PID, no pressure gauge, no
  volumetric stop. The brew switch is manual: the shot runs until it is
  switched off.
- Scale with 1 g resolution for dose and yield. Expect ±1 g noise on every
  reading; do not chase dose differences of 1 to 2 g.
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

- Shot time under ~20 s: grind finer (lower number, towards 1.2).
- Shot time over ~35 s: grind coarser (higher number).
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

## Grinder maintenance (Mazzer Mini Electronic B)

The Mini has no collar lock pin. The chrome adjustment collar is held from
turning a full circle by one small limiter screw on top of the collar.
Thread is reversed: clockwise = coarser = higher numbers = off.

Clearing a jam / cleaning the burr chamber:

1. Unplug. Close the hopper gate, remove the hopper (its safety screws may
   need to come out of the upper burr carrier), empty the beans.
2. Remove the limiter screw from the collar. Keep it safe.
3. Turn the collar clockwise. It unscrews and lifts off with the upper burr
   carrier after several turns. If it binds, put a tamper in the throat and
   press down on the upper carrier to take the load off the thread while
   turning. Do not force or lever it.
4. Brush and vacuum both burrs, the chamber, and both sets of threads.
5. Still unplugged, turn the lower burr by hand. Free = the jam was the
   problem. Will not turn with nothing in the way = motor or bearing
   fault, take it to a technician.
6. Reassemble: mark where the thread engages, start the collar anticlockwise
   without forcing, screw in until it stops gently, back off a full turn,
   refit the limiter screw and hopper.
7. Plug in, run empty, turn finer slowly until the burrs chirp, back off.
   That number is zero. Start espresso about one number coarser.
8. Only ever adjust the collar with the motor running. Never turn it finer
   while stopped with beans in the chamber: that is how it jammed.
9. Zero (chirp) measured 2026-10-21 after reassembly: 1.2.

If the lower burr spins freely by hand but the motor still only hums, the
motor capacitor has failed (common Mazzer fault, cheap part, mains wiring).

Routine cadence for home use (a few shots a day):

- Every few weeks: hopper off, wipe out the oil film, brush the chute.
- Every 2 to 3 months: full strip and clean as above, then re-find zero.
- Signs it is overdue: stiff collar, more clumping, rancid note in the cup.

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
- 2026-10-15: at 3.1, 7.85 s gave 21 g. Six dose checks at 3.1 now scatter
  about ±2 g around the fitted line. Timed dosing on a Mazzer is only good to
  roughly that; the scale adds ±1 g. Treat the run time as coarse and accept
  17 to 21 g for now; it is not what is making shots watery.
- 2026-10-15: grind changed 3.1 -> 3.5 (finer). Run time still 7.85 s. No
  dose or shot at 3.5 yet.
- 2026-10-16: grind moved on to 4.0 (finer) with no shot at 3.5. At 4.0,
  7.85 s gave 23 g. Throughput did not fall with the finer grind; if anything
  it rose. Suggested run time for 18 g: ~6.5 s (slope ~3.6 g/s, i.e. 5 g less
  is ~1.4 s less).
- 2026-10-18: at 4.0, 6.50 s gave 19 g. Shot still watery. Shot time not
  recorded. Moving 3.1 -> 4.0 made no visible difference to the shot or to
  grinder throughput, so small collar steps are not doing enough.
- 2026-10-18: collar moved 4.0 -> 5.0. The collar has to be turned while
  the motor runs, so the grind done during the change is a mix and is not
  logged. First representative data at 5.0 comes from the next grind.
- 2026-10-19: at 5.0, 6.50 s gave 21 g, scooped out to 18 g. Shot: 40 g in
  10 s (4 g/s). Identical to 3.1 and 4.0. Grinder throughput at a given run
  time has also not fallen across 3.1 -> 4.0 -> 5.0. Conclusion: the collar
  moves we have made are not changing the grind enough to matter, or
  something else is bypassing the puck. Stop blind stepping and diagnose.
- Plan, in order:
  1. Basket check: turn the double basket over. Many small holes across the
     bottom = unpressurised, fine. One central hole = pressurised; swap it
     for a plain basket before anything else.
  2. Find the burr zero point ("chirp"): close the hopper gate, run the
     grinder empty of beans, and with the motor running turn the collar
     finer (number up) slowly until the burrs just start to touch and
     chirp. Back off immediately. Note that number as the zero. Espresso
     normally sits about 0.5 to 1.5 numbers coarser than zero. If the chirp
     is at, say, 8, then 5.0 is far too coarse and the right starting point
     is about 7.
  3. Set the collar there, purge a second or two of grounds, re-establish
     the run time for 18 g, pull 36 g and time it.
- 2026-10-21: grinder stopped grinding. Motor hums for the run time but the
  burrs do not turn. Collar now very stiff; turning it coarser did not help.
  Run time had been set to 5.4 s. Most likely the burrs are jammed (closed
  onto packed grounds after the finer moves / chirp attempt). Second
  suspect is the motor start/run capacitor, a common Mazzer failure. Do not
  run the motor while it hums; a stalled motor overheats.
- Plan: unplug, strip and clean the burr chamber (see Grinder maintenance
  section above), reassemble, find zero with the motor running
  and empty, then resume dialling in from about one number coarser than
  zero. Dialling-in data from before the strip-down may not carry over.
- 2026-10-21: grinder stripped, cleaned, reassembled, running again. Lower
  burr turned freely once the chamber was cleared, so the capacitor is fine.
  Chirp found at 1.2. Direction corrected: lower number = finer. The 3.1 ->
  4.0 -> 5.0 moves were all going COARSER, which explains the unchanged 10 s
  shots. All shot data before this date is at coarse, non-espresso settings.
- Plan: restart at 3.0 with 6.5 s run time. Weigh the dose (expect it to
  differ from the pre-strip-down numbers), pull 36 g, time it. Then apply
  the rules of thumb: under 20 s go finer (lower), e.g. 3.0 -> 2.5 -> 2.2.
  Adjust only with the motor running. Record the shot time this time; "watery" alone cannot
  steer the grind. If 5.0 still runs under 20 s, go to 6.0. Also check two
  things that would make grind changes ineffective: (a) the collar is
  actually turning the burr carrier (grounds should visibly look and feel
  finer, closer to flour than sand, and clump); (b) the basket is a plain
  non-pressurised double (many small holes underneath, not one central
  hole).
- 2026-10-25: grind 2.3, run time 8.75 s. Shot time 30.6 s, tasted good.
  First shot inside the 25 to 32 s window. Dose and yield not recorded.
  Run time is up from 6.5 s, but with no dose weighed it is unknown
  whether that gives ~18 g at 2.3. Plan: hold 2.3 and 8.75 s, weigh dose and yield every shot
  to confirm ~18 g in / 36 g out, then tune on taste only.
- 2026-10-26: grind 2.3, dose topped up by hand to 18 g. No coffee at all
  after 20 s of brew. Previous shot at the same grind ran 30.6 s, so either
  the puck choked (dose now higher than on 10-25, which was not weighed, or
  hand-added grounds clumped) or no water is reaching the group (tank empty,
  pump lost prime). Plan: (1) portafilter out, brew switch on: water should
  flow from the group head. If not, fill tank and prime via the steam wand.
  (2) If water flows, it was a choke: knock out and inspect the puck, then
  go coarser 2.3 -> 2.6 at the same 18 g and re-time.
- 2026-10-26: grind 2.6, 9.5 s ground 16 g, topped up to 18 g. Over 60 s
  to 36 g. So water was reaching the group and 10-26's dead shot was a
  choke. Likely explanation for the "good" 30.6 s at 2.3: 8.75 s at 2.3
  probably gave well under 18 g (throughput at 2.6 is only ~1.7 g/s), so
  that shot was underdosed, not dialled in. Plan: grind 2.6 -> 2.9, run
  time ~10 s (throughput rises as grind gets coarser, so re-weigh).
  Prefer adjusting run time over hand top-ups; topped-up grounds sit loose
  on top and can clump. Accept 17 to 19 g.
- 2026-10-27: grind 2.9. 10 s ground short of target; about 1 s more
  needed (~11 s). Shot ~26 s, inside the 25 to 32 s window. Dose and
  yield not confirmed. Plan: hold grind 2.9, set run time 11 s, weigh
  dose and yield, and from here change grind only on taste (sour ->
  finer, bitter -> coarser, 0.1 to 0.2 steps).
- 2026-10-28: grind 2.9, 11 s ground 20 g, levelled off to 18 g. Over
  60 s to 36 g. So the ~26 s shot on 10-27 at the same grind was almost
  certainly underdosed (10 s gave "short", probably 15 to 16 g). Pattern
  so far: at a real 18 g, 2.3 chokes, 2.6 and 2.9 both run over 60 s.
  Shot time is very sensitive to dose, so the dose must be weighed on
  every shot before any grind conclusion is drawn. Plan: hold run time
  11 s (it gives ~20 g at 2.9; expect a bit more at 3.2, so re-weigh and
  trim to ~10.5 s if needed). Grind 2.9 -> 3.2 at 18 g, pull 36 g, time
  it. If still over 35 s, go to 3.5. Levelling off is fine (it removes
  from the top, unlike a hand top-up); the loose-grounds concern only
  applies to adding.
- 2026-10-28: grind 3.2, 11 s ground 20 g, levelled off to 18 g. Shot
  16.4 s (yield assumed 36 g). Same 18 g dose ran over 60 s at 2.9, so
  0.3 on the collar took the shot from >60 s to 16 s. The collar is very
  sensitive in this range: the 25 to 32 s window is somewhere between
  2.9 and 3.2, and 0.1 steps are the right size now. Run time 11 s gives
  20 g at both 2.9 and 3.2, so it is stable; levelling to 18 g is fine.
  Plan: grind 3.2 -> 3.0, run time 11 s, level to 18 g, 36 g out, time
  it. If under 20 s go to 2.95; if over 35 s go to 3.1.

## How Claude should work on this repo

- When the user reports a shot, append a row to `shots.csv` and, if asked,
  suggest the next single change based on the rules above.
- Change one variable at a time between shots unless the user says otherwise.
- Respect the "explicitly NOT tracked" list. Do not relitigate it.
- Keep everything in plain text files in this repo so it survives a machine
  change.
- After any change to a file in this repo, commit and push straight away.
  Do not ask first.
