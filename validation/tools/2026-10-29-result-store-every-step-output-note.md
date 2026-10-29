# Diagnostic observation: every-step native result output

Date: 2026-10-29 (Asia/Shanghai). Context: the new `dem10-stack` verification, retaining 60,004 native frames across four independent native SimulationSession runs.

The current result writer writes a new binary frame, appends its metadata to the frame array, serializes the whole growing manifest, and atomically replaces `run.json` for every appended frame. See `FunDEMSoftware/src/results/resultStore.cpp`, `ResultStoreWriter::append` at lines 492-519, `saveManifest` at lines 84-88, and `atomicWrite` at lines 73-78. SHA256 of the source inspected: `9e28b4cf4eeaf182b4a4abb6447fe520d899d3230afad921f568a147f8efc32c`.

For N retained frames, this creates O(N^2) cumulative manifest serialization and write volume when each manifest entry has bounded size. Binary frame writes themselves remain O(N). Each process has its own writer and lock; concurrent processes can still contend for storage. This is a source-level algorithmic observation, not a profiler measurement attributing all elapsed time to one function.

During this case, both 10,001-frame base runs completed while the 20,001-frame half-step runs continued progressing normally. Every-step binary output therefore costs substantially more than the earlier cases with 1 ms native output plus full-step CSV. The configured cadence, all original frames, solver/library code, and result-store code were retained unchanged. No restart or output-cadence change was used to shorten execution.

This note records evidence for a possible future performance investigation. It does not change the benchmark protocol or any historical case, and no optimization is implemented here.
