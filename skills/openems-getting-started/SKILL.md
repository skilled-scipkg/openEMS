---
name: openems-getting-started
description: This skill should be used when users ask about getting started in openems; it prioritizes documentation references and then source inspection only for unresolved details.
---

# openems: Getting Started

## High-Signal Playbook

### Route conditions
- Use this skill for first-run setup, first simulation execution, boundary/mesh starter choices, and smoke-test verification.
- Route build, linker, packaging, or install-prefix failures to `openems-api-and-scripting`.
- Escalate from docs to code only when behavior is ambiguous; start with `python/openEMS/openEMS.pyx` and `python/openEMS/ports.py`, then core C++ in `openems.cpp` and `openems.h`.
- Primary anchors: `python/README.md`, `python/doc/Tutorials/Intro_Tutorials.rst`, `python/doc/Tutorials/Rect_Waveguide.rst`, `.github/smoketests/python/MSL_NotchFilter.py`, `.github/smoketests/octave/MSL_NotchFilter.m`, `matlab/setup.m`.

### Triage questions
- Are you running Python bindings or Matlab/Octave scripts?
- Is openEMS + CSXCAD already installed in the same prefix?
- Are `OPENEMS_INSTALL_PATH` and `CSXCAD_INSTALL_PATH` set (or a matching `VIRTUAL_ENV` active)?
- Do you want a quick smoke run (`NrTS` low) or a fuller tutorial run?
- Which tutorial geometry is the baseline (Rect waveguide, MSL notch, patch, sphere)?
- What are your boundary conditions and target mesh resolution?
- Is failure at import/build time, simulation setup time, or result validation time?

### Canonical workflow
1. Verify install context and leave repository `python/` before import checks.
2. Confirm imports from a neutral directory: `cd /tmp && python3 -c "import CSXCAD, openEMS; print(openEMS.__version__)"`.
3. Start with smoke scripts (`.github/smoketests/python/MSL_NotchFilter.py` or `.github/smoketests/octave/MSL_NotchFilter.m`) for a fast end-to-end run.
4. Move to a tutorial script (`python/Tutorials/Rect_Waveguide.py` or `matlab/Tutorials/Rect_Waveguide.m`) and keep geometry close to known examples first.
5. Set FDTD core controls early (`NrTS`, excitation, 6-entry BC list) and attach CSX before adding ports.
6. Use moderate mesh resolution first (`lambda/30` to `lambda/50` seen in tutorials/tests), then refine.
7. Run simulation and postprocess with `CalcPort` to inspect `S11/S21` and impedance traces.
8. If setup fails, export XML (`Write2XML`) and inspect mesh/ports before changing physics.
9. Escalate to source maps if docs/tutorial parity is unclear.

### Minimal working example
Python quick-run skeleton (Rectangular waveguide pattern):
```python
import os, tempfile, numpy as np
from CSXCAD import ContinuousStructure
from openEMS import openEMS
from openEMS.physical_constants import C0

sim = os.path.join(tempfile.gettempdir(), "Rect_WG_smoke")
unit = 1e-6
a, b, length = 10700, 4300, 50000
f_start, f0, f_stop = 20e9, 24e9, 26e9
mesh_res = (C0 / f0 / unit) / 30

FDTD = openEMS(NrTS=1000)
FDTD.SetGaussExcite(0.5 * (f_start + f_stop), 0.5 * (f_stop - f_start))
FDTD.SetBoundaryCond([0, 0, 0, 0, "PML_8", "PML_8"])

CSX = ContinuousStructure()
FDTD.SetCSX(CSX)
mesh = CSX.GetGrid()
mesh.SetDeltaUnit(unit)
mesh.AddLine("x", [0, a]); mesh.AddLine("y", [0, b]); mesh.AddLine("z", [0, length])

p1 = FDTD.AddRectWaveGuidePort(0, [0, 0, 10 * mesh_res], [a, b, 15 * mesh_res], "z", a * unit, b * unit, "TE10", 1)
p2 = FDTD.AddRectWaveGuidePort(1, [0, 0, length - 10 * mesh_res], [a, b, length - 15 * mesh_res], "z", a * unit, b * unit, "TE10")
mesh.SmoothMeshLines("all", mesh_res, ratio=1.4)
FDTD.Run(sim, cleanup=True)
f = np.linspace(f_start, f_stop, 201); p1.CalcPort(sim, f); p2.CalcPort(sim, f)
print((p1.uf_ref / p1.uf_inc)[100])
```

Repository-start smoke execution:
```bash
# from repository root
cd /tmp && python3 -c "import CSXCAD, openEMS; print(openEMS.__version__)"
python3 .github/smoketests/python/MSL_NotchFilter.py
octave --silent .github/smoketests/octave/MSL_NotchFilter.m
```

### Pitfalls and fixes
- `RuntimeError` about missing install path vars: set `CSXCAD_INSTALL_PATH` and `OPENEMS_INSTALL_PATH` (or activate matching `VIRTUAL_ENV`) before `pip install .` (`python/README.md`, `python/setup.py`).
- Import errors when testing inside source tree: run import checks outside `python/` (`python/README.md`).
- `Add*Port: CSX is not set!`: call `FDTD.SetCSX(CSX)` before adding ports (`python/openEMS/openEMS.pyx`).
- `Invalid boundary condition size!`: pass exactly six BC entries (`python/openEMS/openEMS.pyx`).
- `At least 5 lines in propagation direction required!` for MSL ports: add/smooth more grid lines before `AddMSLPort` (`python/openEMS/ports.py`).
- Octave helper missing for HDF5 attribute reads: run `matlab/setup.m` in Octave to compile `matlab/h5readatt_octave.cc`.
- Slow/unbounded first runs: use smoke-test style `NrTS=1000` first, then scale up (`.github/smoketests/python/MSL_NotchFilter.py`, `.github/smoketests/octave/MSL_NotchFilter.m`).

### Convergence and validation checks
- Mesh: start around `lambda/30` to `lambda/50`, then check S-parameter stability versus refinement.
- Boundaries: keep absorbing boundaries (`PML_8` or documented alternatives) and enough air padding near radiators/discontinuities.
- Time control: verify `NrTS`/`EndCriteria` are sufficient for decay before trusting frequency-domain results.
- Port sanity: compute `CalcPort` on both ports and confirm passive behavior (`|S11|`, `|S21|` trends match tutorial expectations).
- Regression sanity: rerun one smoke script and at least one tutorial script after environment or parameter changes.
- Run sanity: smoke scripts should finish without exceptions and create simulation outputs under `/tmp` (Python) or local `tmp` folder (Octave tutorial style).

## Scope
- Handle questions about initial setup, quickstarts, and core concepts.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `python/README.md`
- `README`
- `python/doc/index.rst`
- `python/doc/openEMS.rst`
- `python/doc/openEMS_API.rst`
- `python/doc/Tutorials/Simple_Patch_Antenna.rst`
- `python/doc/Tutorials/Rect_Waveguide.rst`
- `python/doc/Tutorials/RCS_Sphere.rst`
- `python/doc/Tutorials/MSL_NotchFilter.rst`
- `python/doc/Tutorials/Helical_Antenna.rst`
- `python/doc/Tutorials/CRLH_Extraction.rst`
- `python/doc/Tutorials/Bent_Patch_Antenna.rst`
- `python/doc/Tutorials/Intro_Tutorials.rst`

## Workflow
- Start with the primary references above.
- If details are missing, inspect `references/doc_map.md` for the complete topic document list.
- Use tutorials/examples as executable usage patterns when available.
- Use tests as behavior or regression references when available.
- If ambiguity remains after docs, inspect `references/source_map.md` and start with the ranked source entry points.
- Cite exact documentation file paths in responses.

## Tutorials and examples
- `matlab/Tutorials`
- `matlab/examples`
- `python/Tutorials`
- `python/doc/Tutorials`

## Test references
- `TESTSUITE`
- `.github/smoketests`
- `python/Tests`

## Optional deeper inspection
- `Analyse`
- `Common`
- `FDTD`
- `cmake`
- `matlab`
- `nf2ff`
- `tools`
- `python/openEMS`

## Source entry points for unresolved issues
- `openems.h`
- `openems.cpp`
- `main.cpp`
- `openEMS.sh`
- `openEMS_MPI.sh`
- `matlab/AddRectWaveGuidePort.m`
- `matlab/AddWaveGuidePort.m`
- `matlab/AddMSLPort.m`
- `matlab/AddCircWaveGuidePort.m`
- `matlab/RunOpenEMS.m`
- `matlab/InitFDTD.m`
- `FDTD/extensions/engine_extension_dispatcher.h`
- `FDTD/openems_fdtd_mpi.h`
- `FDTD/openems_fdtd_mpi.cpp`
- `python/openEMS/openEMS.pyx`
- `python/openEMS/ports.py`
- `python/openEMS/nf2ff.py`
- `python/openEMS/__init__.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" Analyse Common FDTD cmake matlab nf2ff tools python/openEMS`).
