# Model checks, complete runs, and quantitative results

This guide describes the v0.3.40 Workbench workflow. It extends the existing Project, Properties, View, and Output layout with model reports, protected run directories, saved-run playback, quantitative CSV exports, and a headless command. Use the matching verified platform package; an older published runtime may lack these features. These changes are in the Workbench application; the FunDEMBeta contact laws, solvers, and integration algorithms have not been changed.

## Check a project before calculation

Choose **Simulation > Check Model...** (`Ctrl+F5`). The report uses the shared report dialog and contains:

- **Issues**: errors, warnings, and information, including the owning stable object ID and parameter key. Double-click a row to select the owning object; project-wide issues select Loop Parameters or Output.
- **Run & Resources**: particle counts, estimated SDF nodes, current and target step/time, expected new output frames, and a particle-VTU storage estimate.
- **Geometry**: available source mesh checks, surface counts, and estimated grid size, including the sphere-contact padding requirement.
- **Activation & Motion**: Packing/source activation and prescribed-motion start in both steps and seconds. An SPH Jet's length/speed estimate describes its initial column transit time, not a configured injection duration.
- **Material Pairs**: the core's material ownership and combination rules, including mixed Sphere/LS contacts. LS stiffness is per area; damping also depends on effective mass and, for LS contacts, patch area. The preview is limited to the first 512 pairs; input validation still covers all materials.

**Export Report...** saves the report as text. The check reads project definitions and available source data without constructing the solver or generating an SDF. It checks references, checkpoint consistency, enabled force-library paths, counts, schedules, and numeric inputs. Basic source geometry diagnostics do not constitute a complete mesh-quality assessment.

Run and Step invoke this check before preparation. Errors prevent preparation; warnings are reported. The desktop caches the report for the current project and run context. Model changes invalidate it; a changed current step/time, target, or next output boundary requires a new report. Display-only changes do not require a new model check. This is a preparation check, not a repeated scan in every solver step.

Storage numbers are estimates for particle VTU output, not a promise of total disk use. Contacts, Bonds, checkpoints, geometry resources, temporary playback storage, and archive copies add costs. The Sphere time-step heuristic and configured-state SPH limits are guidance, not convergence tests or guaranteed LS/Bond stability limits.

## Run directories and complete frames

**Steps this run** is the requested number of additional DEM steps. The existing JSON key remains `solver.totalStepCount`. A paused unfinished round resumes its current target; another round after completion adds the requested steps to the cumulative step. The preflight report distinguishes the current step, round target, and actual physical time; Live Monitor retains the cumulative step and time.

The configured output directory is a result root. Every new calculation reserves a unique `run-<timestamp>-<suffix>` child. Existing runs are retained. A continuation uses the same child and keeps cumulative frame numbers, steps, and time. The core writes only inside that child's private `.pending` directory.

Each output event prepares a complete numbered bundle before publishing it:

```text
result-root/
  run-<timestamp>-<suffix>/
    project.json
    run-manifest.json
    energy.dat
    <particle-family>.pvd
    frames/
      000000/
        frame.json
        energy.dat
        <scientific output>.vtu
        history/                 playback, checkpoint, and shared-resource references
      000001/
        ...
    history-resources/           immutable resources shared by frame bundles
    .pending/                    unfinished core output; not a complete result frame
```

VTU, the frame's energy row, playback records, and the full checkpoint are closed before the frame bundle is renamed into place. The master manifest describes the committed prefix with explicit `frame`, `step`, and `time`. Playback metadata is published only after the external output transaction succeeds. Shared geometry uses hard links when available, with copying as a fallback.

PVD collections use recorded physical timestamps and point to complete VTUs. Root `energy.dat` retains the scientific solid-system series; each bundle also contains its frame's row. Solid energy excludes SPH-fluid energy. Live monitored energy remains a separate, optional series and can include solver states between output frames.

An interrupted or failed run leaves its completed scientific bundles and manifest on disk. A failure between sidecar publication and the master commit can leave an extra complete bundle or sidecar entry beyond the master prefix. The saved-run exporter rebuilds PVD and root energy output from its authoritative committed history prefix. **Automatic Open Results recovery from a raw run directory has not been implemented**: Open Results accepts the saved archive format below, not an arbitrary `run-*` directory. Inspect retained raw VTUs/metadata directly when an archive was not saved.

**Simulation > Run Statistics...** reports measured initialization, step advancement, snapshot preparation, and output transaction time, along with advanced steps, committed frames, and the actual result directory. These are application-stage measurements, not separate contact-search/force-kernel profiling or a validated performance benchmark.

## Save a run and reopen it

After at least one complete frame exists, choose **File > Save Run...** and select a new directory, conventionally `simulation.fundem-run`. Saving pauses calculation at a safe boundary and exports the committed prefix. The destination must not already exist. Files are staged in a sibling directory, validated, and published by a directory rename; cancellation or failure does not publish a partial destination.

The archive contains:

```text
simulation.fundem-run/
  project.json                   frozen initial run project; mesh sources embedded
  manifest.bin                   archive version, provenance, frame index, and checksums
  history/
    frame-<index>.bin
    checkpoint-<index>.bin
    grids-<resource>.bin
    surfaces-<resource>.bin
  scientific/                    completed VTU/DAT output and regenerated PVD/energy sidecars
```

The archive preserves all recorded render frames, full-precision checkpoints, shared immutable geometry, Packing/source/Bond ID ranges, application/core version information, saved random seeds, and collected monitored solid-energy samples through the last committed frame. Project geometry definitions and triangle mesh data are embedded so playback does not depend on the original mesh file. External particle-force module binaries, SDK files, and implementation source are **not** embedded. Module references remain project metadata; read-only opening does not compile a solver or load those modules. Restarting a model that uses a module still requires a compatible installed library.

The current archive manifest version is 1 and its binary history record version is 4. Readers reject unsupported versions, incompatible byte order, missing records, truncation, changed project bytes, and invalid checksums before installing the results. The default source fingerprint is explicitly labeled `fnv1a64:<hex>`; file/record checksums also use non-cryptographic FNV-1a. They detect accidental corruption and are not authentication or protection against intentional modification. There is no migration reader for arbitrary future or older binary archive formats.

Archive copying, checksums, project reads, and history decoding check cancellation between chunks. Declared manifest text/count lengths are checked against their limits and remaining encoded bytes before allocation. The latest frame is prepared on the loading worker, so installation does not reread a large archive record on the desktop thread. A canceled open keeps the previous document and scene; a canceled save leaves the destination absent. Geometry generation may still need to finish its current underlying construction stage before cancellation is acknowledged.

Choose **File > Open Results...** and select the archive directory. Validation and cancelable scene preparation complete before replacing the current document. The reopened session is results-only: playback, display settings, quantitative queries, frame export, and saving another archive are available; Run and Step are disabled. Playback uses the same bounded cache as live recordings. Closing or replacing this session does not delete the archive files.

Two actions have different meanings:

- **Reset** releases the current playback session and returns to the archive's saved initial project for authoring/calculation. It does not restart from the displayed final frame or delete the saved archive. Any checkpoint restrictions already present in that initial project still apply.
- **File > Export Playback Frame as Project...** creates a restart project from the selected recorded state and chosen Packings/sources. The checkpoint retains source frame provenance, while a newly compiled restart begins at local step/time zero with rebased schedules. Use this when calculation should continue from a particular recorded frame.

During a prepared run, scene geometry, calculated geometry summaries, and Surface & SDF Preview use the frozen run definitions rather than rereading an external OBJ that may have changed or disappeared. Authoring-time cache lookups still verify the current definition/source. Saved archives do not contain measured stage timings or advanced-step work counters; Run Statistics marks these unavailable rather than reporting fictitious zero-cost measurements.

Temporary live history is still removed after Reset/project replacement when active readers release it, and on normal shutdown. Save a run before discarding that history if all frames and scientific files must remain reopenable.

## Quantitative queries and CSV

Choose **Results > Result Query...**. Select a recorded rigid Packing or SPH source, then choose all its particles, one original zero-based local particle index, or an inclusive world-coordinate region of particle centers in metres.

Queries read authoritative, full-precision checkpoint particles one committed frame at a time. They are independent of viewport visibility, clipping, and display sampling. They report selected count, mean position and velocity, minimum/maximum speed, and SPH density/pressure statistics. Averages are arithmetic particle averages, not mass- or volume-weighted averages. A source not yet activated is **Inactive**; an active source with no selected centers is **No match**. Missing or inapplicable statistics are empty cells, not physical zeros. Negative SPH pressure remains a real signed value. Local indices are not renumbered by region selection.

The report has a **Time Series** table and a **Current Frame** particle table. Each preview shows at most 1,000 rows; **Export Time Series...** and **Export Current Particles...** write all selected rows. CSV identifies Packing ID, family, frame, step, physical time, status, and units, uses round-trip numeric precision, and quotes IDs when needed. Cancellation does not publish a partial series.

The query dialog edits a temporary selection: Cancel leaves the caller's selection unchanged. Local indices are bounded by the selected recorded Packing/source range, and the last scope is retained when configuring an existing selection. CSV formatting starts only after a destination is chosen; failed exports preserve an existing destination. User-requested background cancellation is reported as canceled, including when a file reader signals it with an exception; genuine worker/progress failures remain failures.

**Results > Export Solid Energy...** (also available under File) exports collected monitored energy with `step`, `time_s`, component energies in joules, and their total. Enable **Monitor solid energy** before running to collect this series. This export does not infer missing samples from `energy.dat`, and its solid energies exclude SPH fluid.

## Headless checks and runs

The Qt Core-only `FunDEM-cli` executable uses the same serializer, preflight, CPU session, protected output, and archive writer without a window or OpenGL context. Use the executable supplied with the matching v0.3.40 platform package; this guide does not assert that an older published runtime already contains it.

```text
FunDEM-cli --check project.fundem.json
FunDEM-cli --run project.fundem.json [--steps N --output root --archive newdir]
```

For example, on Windows PowerShell:

```powershell
.\FunDEM-cli.exe --check .\examples\gombocSelfRighting.fundem.json
.\FunDEM-cli.exe --run .\project.fundem.json --steps 100 --output C:\FunDEM-results --archive C:\FunDEM-results\test.fundem-run
```

`--check` prints a JSON preflight report on stdout, does not compile a solver, and does not generate output. It rejects run-only overrides. `--run` checks first, then performs the configured calculation. `--steps` must be a positive supported integer; overrides are in memory and do not rewrite the project. `--output` chooses the parent root; a unique child is reserved. `--archive` saves a new archive after successful completion and must name a nonexistent destination. Preparation, progress, and the actual result path are written to stderr; stdout includes the check report and a completion message.

Exit codes are 0 for success, 2 for invalid command combinations, 3 for preflight errors, 4 for load/preparation/run/archive failure, and 130 for a handled interruption. Ctrl+C requests a safe pause and retains committed scientific frames; it does not automatically save an archive.

Malformed or nonpositive `--steps`, empty destination arguments, and an already existing `--archive` destination are rejected with exit code 2 before compiling or calculating. The final archive writer also refuses replacement, including if another process creates that destination after the argument check.

## Work that remains planned

The implemented query interface is tabular with CSV export; query charts, cross-run energy-curve comparison, richer force/contact/energy queries, and a parameter-scan GUI are not complete. Full mesh-quality inspection, Packing overlap/porosity diagnostics, geometry problem highlighting, separate contact-search/force-phase profiling, physical convergence suites, and repeatable performance baselines also remain planned. The activation report is a table rather than a graphical timeline. Example/help browsers and other later roadmap items are not implied by the features documented here.
