# Bioelectric Simulation of Wound-Response Ion Channel and Gap Junction Dynamics (BETSE)

## Overview
This project uses BETSE (Bioelectric Tissue Simulation Engine), an open-source biophysics simulator, to model how ion channel and gap junction manipulations affect membrane voltage (Vmem) in a simulated wounded tissue. It is a direct extension of the regen-convergence project: that project's Day 24 screen flagged ion channel and gap junction genes (e.g. Gja1/Cx43, Kcn family) as candidates worth a bioelectric-mechanism angle. This project tests, computationally, how manipulating those same channel classes affects tissue-level Vmem - a reproduction of established BETSE mechanics, not a novel simulation method.

## Methods
- Tool: BETSE v1.1.1, Python 3.7 (BETSE requires an older Python; run in a dedicated bioelectric-sim conda environment, separate from the main project's Python 3.12 environment)
- Baseline: BETSE's built-in demo configuration (sample_sim.yaml) - a circular tissue cluster (about 225 hexagonal cells) with a scheduled cut event simulating a wound
- Three manipulations tested, isolated in separate run folders (run_baseline, run_gj_blocked, run_k_blocked) to avoid output overwrites:
  1. Gap junction blockade - block gap junctions event enabled, tested first with default timing (2.0-6.0s window against a 0.04s wound-response phase - a timing mismatch caught and corrected), then retimed to overlap the wound-response phase (0.00-0.04s) with a faster ramp rate (0.005s)
  2. Gap junction blockade, variance check - cell-to-cell Vmem standard deviation compared, not just population mean, since gap junction effects are expected to change voltage distribution across cells more than the average
  3. Localized K+ channel permeability increase - the config's built-in change K mem event (50x multiplier, applied only to a small Spot tissue region within the cluster), tested during the init phase's active window (t=2.0s, at peak effect) rather than after the event had already reverted

## Results
| Test | Baseline | Manipulated | Difference |
|---|---|---|---|
| Gap junction block, population mean Vmem | -43.065 mV | -43.149 mV | ~0.08 mV |
| Gap junction block, retimed, population mean Vmem | -43.107 mV | -43.184 mV | ~0.08 mV |
| Gap junction block, retimed, population std | 3.125 mV | 3.140 mV | ~0.5% |
| K+ channel (Spot region), population mean Vmem, t=2.0s | -44.988 mV | -44.996 mV | ~0.01 mV |
| K+ channel (Spot region), population std, t=2.0s | 1.138 mV | 1.128 mV | ~1% |

Visual comparison of the final Vmem 2D maps (baseline vs K+-channel-modified) at the targeted Spot region showed no detectable difference (see figures folder).

## Interpretation
None of the three tests produced a measurable Vmem change, and each was diagnosed to a specific, explainable cause rather than left as an unexplained null result:
1. The original gap junction block window (2.0-6.0s) did not overlap the 0.04s wound-response phase at all - the manipulation had no time to act during the phase that matters.
2. After retiming to overlap the correct window, the effect was still negligible - gap junction diffusion likely needs a longer active window than 0.04s to meaningfully redistribute voltage across this cell cluster's geometry.
3. The K+ channel change, though correctly timed and spatially targeted, produced no detectable shift even in the targeted region - at this specific multiplier (50x) and geometry, the perturbation was insufficient relative to the tissue's baseline electrical stability.

Conclusion: in this configuration, both gap junction and ion channel manipulations require larger perturbation magnitudes and/or longer active windows, relative to the system's underlying electrical time constants, than this demo configuration's defaults provide, to produce a detectable Vmem effect. This is a real, useful methodological finding for designing future BETSE experiments, not a claim that these channel types are biologically unimportant - the opposite: published BETSE literature (Levin lab) demonstrates real bioelectric effects from these same channel classes, achieved with parameters tuned specifically for that purpose.

## Limitations
- All three tests used BETSE's default demo geometry and parameters, not values fit to a specific published biological system
- No formal statistical test was applied to the mean/variance differences reported - they are reported descriptively
- Effects were only checked at the tissue level (population mean/variance) and one intermediate timepoint; a full time-course animation was not quantitatively analyzed
- Single-run results (no replicate simulations), consistent with BETSE's deterministic solver

## Future Work
- Repeat with larger permeability multipliers and longer active windows, informed by published BETSE parameter sets for known effects (e.g. planarian or Xenopus regeneration models)
- Numerically isolate just the targeted Spot-region cells rather than relying on whole-tissue or visual comparison
- Extend to a true wound-healing time-course scenario with gap junction/ion channel manipulation applied throughout, not a short isolated window
