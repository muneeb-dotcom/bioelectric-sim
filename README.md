# Bioelectric Simulation of Wound-Response Ion Channels and Gap Junctions (BETSE)

Simulations with BETSE (Bioelectric Tissue Simulation Engine) testing how gap junction and K⁺ channel manipulations affect membrane voltage (Vmem) in a simulated wounded tissue. This is a direct extension of the [regen-convergence-v2](https://github.com/muneeb-dotcom/regen-convergence-v2) project, whose screen flagged ion channel and gap junction genes (e.g. Gja1/Cx43, Kcn family) as candidates for a bioelectric angle. It reproduces established BETSE mechanics and is not a novel simulation method.

## Summary of Results

| Test | Baseline | Manipulated | Difference |
|---|---|---|---|
| Gap junction block, population mean Vmem | -43.065 mV | -43.149 mV | ~0.08 mV |
| Gap junction block (retimed), population mean Vmem | -43.107 mV | -43.184 mV | ~0.08 mV |
| Gap junction block (retimed), population std | 3.125 mV | 3.140 mV | ~0.5% |
| K⁺ channel (Spot region), mean Vmem at t = 2.0 s | -44.988 mV | -44.996 mV | ~0.01 mV |
| K⁺ channel (Spot region), std at t = 2.0 s | 1.138 mV | 1.128 mV | ~1% |

None of the three manipulations produced a measurable Vmem change, and each null result was traced to a specific cause:

1. The default gap junction block window (2.0-6.0 s) did not overlap the 0.04 s wound-response phase at all.
2. After retiming to overlap that phase, the effect was still negligible, probably because gap junction diffusion needs a longer window to redistribute voltage across this geometry.
3. The K⁺ change (50x permeability in a small Spot region), correctly timed and targeted, produced no detectable shift against the tissue's baseline electrical stability.

Takeaway: in this demo configuration, both channel classes need larger perturbations and/or longer active windows than the defaults provide. This is a methodological finding for designing future BETSE experiments, not a claim that these channels are biologically unimportant.

## Methods

- Tool: BETSE v1.1.1 on Python 3.7, in a dedicated `bioelectric-sim` conda environment separate from the main project environment
- Baseline: BETSE's built-in demo configuration (`sample_sim.yaml`), a circular cluster of about 225 hexagonal cells with a scheduled cut event simulating a wound
- Each manipulation is run in its own folder to avoid output overwrites
- Gap junction blockade: tested with default timing, then retimed to the wound-response phase (0.00-0.04 s) with a faster ramp rate (0.005 s); cell-to-cell Vmem standard deviation was also compared, not just the mean
- Localized K⁺ channel permeability increase: the config's built-in `change K mem` event (50x, small Spot region), checked at t = 2.0 s during its active window

## Repository Structure

```text
bioelectric-sim/
├── docs/
│   └── writeup.md      # full write-up: methods, results, interpretation, limitations
├── run_baseline/       # baseline simulation run
└── run_k_blocked/      # localized K+ channel manipulation run
```

## Limitations

- Default demo geometry and parameters, not values fit to a specific published biological system
- Differences are reported descriptively, with no formal statistical test
- Effects were checked only at tissue level and one intermediate timepoint
- Single-run results (no replicates), consistent with BETSE's deterministic solver

## Future Work

- Repeat with larger permeability multipliers and longer active windows, informed by published BETSE parameter sets (e.g. planarian or Xenopus regeneration models)
- Isolate the targeted Spot-region cells numerically instead of relying on whole-tissue or visual comparison
- Extend to a true wound-healing time course with manipulations applied throughout
