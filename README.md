# coffee

brew optimisoooor

Dialling in espresso on a Mazzer grinder and a Rancilio Silvia. Every shot is
logged in `shots.csv`. Full context, procedure and conventions are in
`CLAUDE.md`.

## Independent variables (we set these)

| variable | unit | notes |
| --- | --- | --- |
| grind setting | number on Mazzer collar, 0.0 to 10.0 | main lever for shot time |
| grinder run time | seconds | lever for hitting target dose at a given grind |
| yield | grams in cup | shot is stopped on weight |

Derived from these: dose (grams in portafilter) and brew ratio (yield / dose).

## Dependent variables (we measure these)

| variable | unit | notes |
| --- | --- | --- |
| dose | grams | result of grind setting and run time |
| shot time | seconds | brew switch on to off |
| flow rate | g/s | yield / shot time |
| sour_bitter | 1 to 5 | 1 sour, 3 balanced, 5 bitter |
| body | 1 to 5 | 1 thin and watery, 5 syrupy |
| overall | 1 to 10 | |

## Not tracked

Roast date, hopper fill level. Grinder run time is not held constant.

## Current state

Grind setting 3.1, bean Supreme / Supreme. 9.00 s gave 22 g. Holding grind at
3.1 and reducing run time until dose is 18 g.
