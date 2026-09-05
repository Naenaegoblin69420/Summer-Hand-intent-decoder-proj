# CubeAI and Large-memory deployment handoff

The six highlighted per-session best folds are converted and host-validated.
Midsize and Large share each neural package, so there are six unique CubeAI
packages rather than twelve copies.

## Ready now

- Six INT8-encoder + FP32-GRU/head CubeAI packages under
  `midsize/<session>/cubeai/fold-<best>/`.
- Six Large reference manifests under `large/<session>/cubeai/manifest.json`.
- Full GRU state output `[1, 50, 64]`; hidden[49] is row 49 and can seed the
  new residual-memory query.
- Generated-C held-out replay passed all six accuracy gates. The selected-fold
  diagnostic mean is 0.7941, while official reporting remains **0.7411 ±
  0.0656 across all 30 folds** for Midsize.
- Phase-15 exact PC KNN replay completed all 30 fold-specific GRU-hidden banks.
  Large reached **0.7498 ± 0.0632**, or **+0.0086 R²** versus bank ABSENT.

## Completed deployment sequence

1. Converted all six highlighted best-fold PC banks to `BCIMEM1` with 256 IVF
   clusters, 32 probes, INT8 keys, and FP16 residuals.
2. Replayed the packed files with the CM7 search policy and recorded
   exact-versus-IVF parity in
   `../experiment/phase15_large_memory_validation/results/phase15_firmware_ivf_bestfolds.json`.
3. Integrated all six banks in GUI branch `deliverable3` and the loader,
   query, search, and fallback path in firmware branch `AI`.
4. Preserved the paper result over all 30 folds rather than replacing it with
   the six test-selected deployment folds.

## Remaining gates

1. Run Large bank-ABSENT versus READY parity and latency on the STM32 board.
2. Re-run Midsize dataset replay after the latest FIFO scheduling fix and
   confirm coalesced predictions remain zero.
3. Convert the other 24 folds only if a future study requires all 30 folds on
   hardware; they are not required for the six-session demonstrator.

CubeAI 10.2 cannot import GRU `return_state`, and its Keras importer fails on
`Cropping1D`. The active ABI therefore exposes the complete GRU state sequence
and reads timestep 49 without recomputing the GRU. This adds a 12.8 KB float32
output view/buffer requirement included in the MCU RAM map. The deployed graph
therefore exposes both the 50 × 2 velocity sequence and 50 × 64 hidden sequence.
