# GTNH-CropAutomation — 31/31/31 fork

A modified copy of [DylanTaylor1/GTNH-CropAutomation](https://github.com/DylanTaylor1/GTNH-CropAutomation),
an OpenComputers robot that automatically tiers-up, stats-up, and spreads (duplicates)
IC2 crops in GregTech: New Horizons.

This fork retunes the breeding target to **31/31/31** (max Growth / Gain / Resistance)
instead of the upstream **31/31/0**.

## Why 31/31/31?

In **GTNH 2.9**, IC2 Crops are replaced by **CropsNH** (a fork of Agricraft). In
CropsNH, resistance is beneficial — a max-resistance crop yields more seeds and
always passes its checks — so **31/31/31 becomes the optimal crop**, not 31/31/0.
Existing IC2 crops migrate to their CropsNH equivalents when a world is upgraded,
so breeding 31/31/31 in 2.8 now is a head start for 2.9.

**Caveats:**
- In 2.8 (IC2), a resistance of 31 grows slightly slower and gives **no** benefit in
  the Extreme Industrial Greenhouse. The payoff is realized only after upgrading to 2.9.
- This bot is **IC2-only** — it scans `IC2:blockCrop`. It is meant for breeding
  *before* the update. It does **not** drive CropsNH after you upgrade (CropsNH breeds
  via doubled crop sticks and the Industrial Farm instead).

## What changed vs. upstream

**config.lua**

| setting | upstream | this fork |
|---|---|---|
| `workingMaxGrowth` | 21 | 31 |
| `workingMaxResistance` | 2 | 31 |
| `storageMaxGrowth` | 23 | 31 |
| `storageMaxResistance` | 2 | 31 |
| `autoStatThreshold` | 52 | 93 |
| `autoSpreadThreshold` | 50 | 93 |

**autoStat.lua / autoSpread.lua / autoTier.lua**

The stat-scoring formula changed from `gr + ga - re` to `gr + ga + re`, so the bot
optimizes *toward* high resistance instead of away from it. The threshold and the
formula must always match — that is why the thresholds are `93` (31 + 31 + 31).

A threshold of 93 means the bot keeps breeding until **every** working-farm crop is a
perfect 31/31/31, capped by `maxBreedRound`. If that is too slow, lower
`autoStatThreshold` / `autoSpreadThreshold` and filter the resulting seed bags by stat
afterward (NEI/AE2 regex search works on Gr/Ga/Re).

`scanner.lua` is unchanged: its weed check reads the config caps directly, so raising
the caps above is what stops the bot from treating your high-stat crops as weeds.

## Install

On the robot:

```
wget https://raw.githubusercontent.com/maustin44/GTNH-CropAutomation-313131/main/setup.lua && setup
```

Then run one of `autoTier`, `autoStat`, `autoSpread` (or `autoStat && autoSpread`).

## Build, wiring, and farm layout

The hardware build, robot assembly, farm/dislocator layout, and troubleshooting are
identical to upstream and are **not** duplicated here. See the original guide:
<https://github.com/DylanTaylor1/GTNH-CropAutomation> and
<https://wiki.gtnewhorizons.com/wiki/Open_Computers_Crop_Breeding>.

To read a full error message on the robot:

```
autoStat 2>/errors.log
edit /errors.log
```

## Credits

All original work by **DylanTaylor1**, building on huchenlei and xyqyear's
implementations, with contributions from Mozzg, Mderoulou, and Sargates. This fork only
changes the breeding target.
