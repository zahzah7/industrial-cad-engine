# Practical Roadmap: SAM 8.5 Competitor

## Objective and priorities

Build a dependable, closed-source commercial desktop application competing with **SAM 8.5 Professional** in planar mechanism design, synthesis, prescribed-motion analysis, force analysis, and optimization.

Priorities, in order:

1. Correct, useful engineering functionality.
2. Reliability, understandable errors, and preservation of the user's work.
3. Fast, practical workflows.
4. A minimalistic, modern, aesthetically pleasing interface.

**Complete and validate the backend before implementing the application UI.** The backend includes all required engineering calculations, design tools, project storage, and data exchange. It must work through a headless runner without a window or graphics context.

Default delivery target: Windows x64 desktop, with local/offline operation. Keep the core portable where inexpensive; additional platforms are separate delivery work.

The roadmap defines observable outcomes, not time estimates. Progress is demonstrated by working software and verification evidence.

## Implementation approach

- Use a single application codebase with an in-process C++ backend and a reproducible build. Add a desktop interface after the backend passes its completion check.
- Use **Chrono** for mechanism solving, **Ceres** for suitable local fitting, and **NLopt** for global/local constrained search. Integrate features when needed rather than building elaborate wrappers for every possible library capability.
- Start with one complete mechanism workflow, then extend the same implementation. Avoid disconnected demonstrations that cannot become the application.
- Keep the editable project model independent of Chrono and the UI. Store engineering intent rather than serialized solver internals.
- Use one analysis pipeline for normal analysis, optimization, validation, reports, and the eventual UI.
- Choose mature libraries for UI controls, plotting, expressions, and DXF handling where practical. Check capabilities and distribution terms before adoption.
- Plan only the next manageable implementation tasks in detail. Introduce abstractions, actual file migrations, caching, and parallelism when a demonstrated need warrants them.
- Keep this roadmap and one concise progress log current. Record completed work, verification commands, important decisions, remaining issues, and the next task. Avoid creating extra planning documents without a purpose.
- Make routine implementation decisions autonomously within the agreed scope. Milestone checks require evidence; they do not require repeated user approval.
- Fix a failure before building functionality that depends on it. Independent backend work may proceed in parallel when its dependencies are ready.
- Do not continually polish passing work while required functionality remains unfinished.

### Working through longer assignments

An assignment may cover several milestones. Break it into smaller tasks internally, implement and verify each, and continue while the authorized scope remains unfinished. Preserve a runnable checkpoint when interrupted or when a work budget ends. Report completed, partially completed, and blocked work accurately. Finishing early is preferable to consuming a time allowance unnecessarily.

## Target feature checklist

The final target retains SAM 8.5 Professional functionality. The early working backend is an implementation checkpoint, not a reduction of the final scope.

| Area | Required capability |
| --- | --- |
| Mechanisms | Planar open chains, closed loops, multiple loops, rigid groups, and supported planetary arrangements |
| Components | Rigid links/beams, straight and curved sliders, gears, belts/pulleys, rack-and-pinion, and sensors |
| Force elements | Translational/rotational springs, dampers, friction, nonlinear springs, and gas springs |
| Mass and loads | Mass, center of mass, inertia, gravity, and supported applied forces/torques |
| Motion inputs | Up to ten independent inputs; absolute/relative displacement or rotation, actuator extension, segmented laws, and imported data |
| Motion results | Absolute/relative position, displacement, angles, velocity, acceleration, paths, and velocity-vector traces |
| Force results | Drive force/torque, bearing reactions, internal/gear forces, and power |
| Expressions | Scalar/time-series formulas, logical operations, extrema, mean, RMS, integration, differentiation, distances, and clearances |
| Synthesis | Four-bar angle-function and three-position synthesis; documented exact/approximate straight-line templates |
| Optimization | Bounded design variables, target paths with/without timing, curve matching, constrained objectives, global exploration, local refinement, and candidate comparison |
| Data and graphics | Editable saved projects, tabulated inputs/results, supported DXF entities, graphical attachments, and useful exports |
| Desktop workflows | Mechanism editing, motion editing, animation, plots, synthesis, optimization, save/reopen, and export |

Track each area against a concrete example and verification result as it is implemented. Resolve ambiguities against the SAM manual and documented examples; do not claim exact behavioral equivalence without checking it.

This target includes SAM-style cam-drive modeling through curved sliders. General 3D mechanisms, structural FEA, unrestricted impact simulation, advanced physical cam-envelope synthesis, manufacturing toolpaths, cloud collaboration, and native SAM project-file compatibility are outside this project unless separately requested.

## Milestone 1 — A working and verified backend foundation

**Outcome:** a small real backend can load a mechanism, analyze it, and produce useful results.

### Build

- [ ] Establish the repository, reproducible build, test runner, and pinned dependency configurations.
- [ ] Build a minimal Chrono integration that supports a prescribed-motion four-bar and slider-crank.
- [ ] Add a small editable project model: stable IDs, geometry, connections, supports, drive, masses, loads, units, and settings needed by these examples.
- [ ] Save/load that model using a simple versioned format. Start with a version marker; implement migrations when an actual format change requires them.
- [ ] Provide a small headless runner that loads a project, validates it, analyzes a complete cycle, and exports results.
- [ ] Obtain positions, velocities, accelerations, drive torque, and reactions from the actual solver.
- [ ] Exercise Ceres and NLopt on small representative fitting/search problems to confirm integration. Build out the product's optimization workflow in Milestone 3.
- [ ] Check prospective desktop-toolkit, plotting, expression, and DXF options for integration and licensing feasibility without implementing application screens.

### Verify

Compare motion against an analytical or independently formulated example. Check a simple load case and confirm that saved/reopened projects reproduce the results. Include one invalid mechanism and a meaningful error.

The calculation must answer: **what motion and required forces result from this prescribed drive?** A plausible animation or a forward-dynamics run is insufficient evidence.

### Dependency and license decisions

Use one supported dependency-build route. Record the versions and options actually used.

For the permissive NLopt configuration discussed, use **NLOPT_LUKSAN=OFF** and verify that the distributed build excludes those LGPL routines. NLopt 2.11.0 documents the remaining configuration under MIT terms. Preserve all applicable notices, including component notices. Recheck this against the version adopted.

**Done when:** a clean build can run both example mechanisms headlessly, save/reopen them, export checked motion/load results, and report the invalid example correctly.

## Milestone 2 — Complete the mechanism-analysis backend

**Outcome:** the required mechanism, motion, force, measurement, and file capabilities work through the same backend.

Extend the working model only as each capability needs it. Add a representative example and appropriate verification with every new component or calculation.

### Mechanisms and components

- [ ] Add multi-node rigid bodies and multiple loops while preserving physical bearing and rigid-group identities.
- [ ] Add external/internal gears, carrier motion and planetary arrangements, belts/pulleys, and rack-and-pinion.
- [ ] Add fixed/moving curved-slider guides using open/closed piecewise Bezier curves, curve import, and tracking along the curve.
- [ ] Add translational/rotational springs, dampers, friction, nonlinear springs, gas springs, and remaining supported sensors.
- [ ] Define each component's motion, load, and measurement behavior. Use existing Chrono primitives where they match; add compositions, force laws, or constraints where they do not.

A gear-ratio constraint does not by itself establish the correct mesh and bearing loads. Validate pressure-angle conventions, load directions, and force reconstruction. Curved sliders require the supported curve-distance measurements and friction behavior as well as the right trajectory.

### Motion, forces, and reliability

- [ ] Add the required motion laws and segmented profiles, imported tabulated inputs, correct derivatives, and continuity feedback.
- [ ] Support up to ten independent drives with consistent timing and absolute/relative conventions.
- [ ] Add gravity and supported time-dependent or kinematics-dependent loads.
- [ ] Produce drive torque/force, physical bearing reactions, internal loads, gear forces, and power with documented units, frames, and signs.
- [ ] Preserve the intended assembly configuration through the cycle; detect unreachable positions, branch changes, singularities, and insufficient/inconsistent/redundant constraints.
- [ ] Return explicit numerical failure or unreliable-result status. Do not silently present an unconverged calculation as valid.

### Results, geometry, and files

- [ ] Add absolute/relative measurements, paths, velocity traces, and results against time or another quantity.
- [ ] Add expressions with dependency ordering, extrema, mean/RMS, integration/differentiation, and useful errors.
- [ ] Add supported point/line/curve/shape construction and distance/clearance calculations.
- [ ] Import/export the required DXF entity subset and attach graphical groups to moving elements.
- [ ] Preserve graphics, curves, inputs, settings, and attachments in project files.
- [ ] Export selected result tables, animation-ready data, and useful project documentation.

**Done when:** every checklist area owned by this milestone has an isolated verification example plus a representative combined-mechanism example. Complete-cycle motion and force results are checked; invalid cases receive actionable diagnostics. All operations remain available without UI code.

## Milestone 3 — Complete synthesis and optimization

**Outcome:** the backend helps design mechanisms and improve them, rather than only analyze existing geometry.

Begin this work as soon as the required model, measurements, and analysis capabilities from Milestone 2 are available. Unrelated component work does not have to finish first.

### Synthesis

- [ ] Implement four-bar synthesis from input/output-angle requirements and three prescribed coupler positions/orientations, including the documented wizard variants.
- [ ] Implement exact straight-line templates: symmetric slider-crank, geared double-crank, hypocyclic planetary gear, planetary gear-coupled dyad, and belt-coupled dyad.
- [ ] Implement approximate straight-line templates: Watt, Chebyshev, Roberts, Evans, and Hoecken.
- [ ] Return ordinary editable project models, valid solution choices, usable parameter ranges, and fitting error where approximation is involved.
- [ ] Reject infeasible requirements clearly and check generated mechanisms over their intended motion range.

Use analytical recipes or numerical fitting as appropriate. Ceres does not supply the mechanical synthesis recipes.

### Optimization

- [ ] Bind design variables to geometry, pivot coordinates, masses/locations, spring properties, ratios, and the other supported parameters.
- [ ] Support bounds/scaling and reliable rebuilding and evaluation of candidate mechanisms.
- [ ] Implement timed and untimed target-path matching with explicit correspondence rules.
- [ ] Implement motion/force curve matching, peak/RMS/mean objectives, user expressions, and the required design constraints.
- [ ] Evaluate constraints throughout the relevant cycle and check whether sampling can miss a peak or clearance violation.
- [ ] Handle failed, infeasible, and numerically unreliable candidates explicitly; they must never win because evaluation failed.
- [ ] Implement global exploration and local refinement using suitable NLopt algorithms. Evaluate ISRES and COBYLA first; use other methods when evidence supports them.
- [ ] Use Ceres where smooth least-squares fitting is beneficial. Do not assume automatic differentiation through Chrono.
- [ ] Support reproducible seeds, run/evaluation budgets, progress, cancellation, best valid candidates, and manual or automatic refinement.
- [ ] Re-analyze accepted candidates at adequate accuracy and verify their constraints.

Implement the required workflow first. Add caching or parallel candidate evaluation only when profiling shows a useful benefit.

**Done when:** the required synthesis examples meet their position/angle requirements; a path-fitting example improves its path; a counterweight example reduces the chosen torque measure; and a constrained example remains feasible. Results reproduce from saved inputs, seeds, and settings. Cancellation preserves completed valid results.

## Milestone 4 — Backend completion check

**Outcome:** the complete engineering backend is functional and trustworthy enough to support the application.

This is a completion check on continuously tested work, not a separate effort to start testing late.

### Required evidence

- [ ] Every backend capability in the target checklist is implemented, callable headlessly, and linked to a verification example.
- [ ] Complete workflows cover modeling, loading/saving, analysis, measurements, synthesis, optimization, DXF/data exchange, and exports.
- [ ] Numerical checks include analytical/reference motion, force and moment balances, applicable power relationships, and convergence/sampling studies.
- [ ] Tests cover alternate assemblies, difficult positions, invalid dimensions/drives, poorly scaled models, and failed optimization candidates.
- [ ] Quantity-specific tolerances reflect units, model scale, and expected accuracy. Reference values must not simply be generated by the code being tested.
- [ ] Acceleration and reaction accuracy are checked using suitable Chrono solver settings.
- [ ] Representative projects establish runtime/memory baselines on declared hardware. Address measured bottlenecks; do not invent arbitrary performance guarantees.
- [ ] Project files round-trip correctly, results have clear validity/provenance, and lengthy operations support progress and cancellation.
- [ ] No known defect invalidates a required result or prevents a principal backend workflow.

Compare with licensed SAM runs where available. Record what was independently validated and what was directly compared; do not imply a comparison occurred when it did not.

**Backend done means all required backend functionality passes this check. It does not mean every theoretically possible mechanism is solvable or that backend code can never change again.**

**Do not start implementing application screens before this milestone passes.** If later UI work exposes a backend defect, fix it and add the appropriate regression check before relying on that behavior.

## Milestone 5 — Build the complete practical interface

**Outcome:** a user can perform the principal engineering tasks without a terminal.

Use the toolkit already assessed for feasibility. Develop directly against the completed backend. Keep all engineering calculations in that backend.

### Workspace and interaction

- [ ] Make the mechanism canvas the dominant surface, with a compact tool/component area, contextual properties, timeline controls, and collapsible results.
- [ ] Implement selection, direct manipulation, pan/zoom, snapping, dimensions, precise numeric input with units, and undo/redo.
- [ ] Add motion-law editing, animation, scrubbing, traces, linked plots, and engineering-result inspection.
- [ ] Add synthesis forms/solution choices, optimization variables/objectives/constraints, progress, and candidate comparison.
- [ ] Add expressions, measurements, DXF workflows, save/reopen, and exports.
- [ ] Keep analysis and optimization off the interaction thread, with visible progress and cancellation.
- [ ] Display actionable diagnostics beside the relevant model objects. Protect unsaved work and identify results that are stale after an edit.

### Visual quality

The UI must be **minimalistic, modern, aesthetically pleasing, and practical**:

- Clear typography, consistent spacing/alignment, readable dimensions, and restrained color.
- Crisp vector drawings, accessible contrast, visible selection, and understandable state indicators.
- Common actions easy to find; advanced controls accessible without occupying the entire workspace.
- Predictable shortcuts and efficient keyboard/numeric workflows.
- A coherent default theme that works at normal window sizes and display scaling.
- Visual polish that supports the work: avoid decorative dashboards, oversized controls, and unnecessary effects.

Implement a complete four-bar editing/analysis workflow first, then connect the remaining finished backend tools. Refine the layout using actual tasks rather than spending an extended phase producing mockups.

**Done when:** users can build/edit a mechanism, define motion, inspect torque, use a synthesis wizard, optimize and compare candidates, and save/reopen/export through the interface. UI results match headless results. The interface stays responsive and errors do not destroy work.

## Milestone 6 — Verify the application and deliver a usable release

**Outcome:** an installable application that performs its stated functions reliably.

- [ ] Run the numerical examples and principal end-to-end desktop workflows on the supported Windows configurations.
- [ ] Exercise realistic projects and editing sessions, including repeated edits, undo/redo, cancellation, saving, and reopening.
- [ ] Fix correctness defects, lost-work risks, broken workflows, and significant performance/usability problems before cosmetic refinements.
- [ ] Verify installation, clean-machine dependencies, project-file handling, and offline operation.
- [ ] Supply useful example projects, concise help, documented component assumptions, and known limitations.
- [ ] Package the notices/materials required by the actual distributed dependencies.
- [ ] Have representative mechanical designers or educators try the principal tasks when available; record and address their findings.
- [ ] Complete the feature checklist with evidence and resolve missing required functions before claiming the planned SAM 8.5 coverage.

Do not add activation systems, cloud services, plugin frameworks, auto-updaters, extra platforms, or unrelated product features unless requested or required for the agreed release.

**Done when:** a user can install the application and complete the target workflows with checked results, clear feedback, and no development environment or terminal.

## Implementation order at a glance

1. Make one complete backend workflow work and verify it.
2. Expand mechanism analysis, components, results, and files.
3. Add synthesis and optimization as their dependencies become ready.
4. Confirm the complete backend passes its checks.
5. Build and refine the UI over that backend.
6. Fix remaining application defects, package, and deliver.

The hard sequencing requirement is **backend before UI**. Within backend development, follow actual dependencies and keep working increments runnable.

## Reference material

Use these to resolve feature details and implementation questions. Check documentation against the versions actually adopted.

- [SAM 8.5 manual](https://www.artas.nl/en/downloads/download/242_12b79921aba37dd740c2be1af3efc7d9)
- [SAM synthesis wizards](https://www.artas.nl/en/sam/features/design-wizards)
- [SAM analysis and expressions](https://www.artas.nl/en/sam/features/analysis)
- [SAM optimization](https://www.artas.nl/en/sam/features/optimization)
- [SAM curved-slider scope](https://www.artas.nl/en/curved-slider)
- [SAM force-analysis semantics](https://www.artas.nl/en/support/video-tutorials/43-what-is-the-difference-between-kinetostatics-and-dynamics)
- [Chrono capabilities](https://api.chrono.projectchrono.org/introduction_chrono.html)
- [Chrono assembly and analysis](https://api.chrono.projectchrono.org/classchrono_1_1_ch_system.html)
- [Ceres modeling limitations](https://ceres-solver.org/modeling_faqs.html)
- [NLopt algorithms](https://nlopt.readthedocs.io/en/stable/NLopt_Algorithms/)
- [NLopt 2.11.0 license](https://github.com/stevengj/nlopt/blob/v2.11.0/COPYING)
- [NLopt 2.11.0 build configuration](https://github.com/stevengj/nlopt/blob/v2.11.0/CMakeLists.txt)
