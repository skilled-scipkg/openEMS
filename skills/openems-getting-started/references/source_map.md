# openems source map: Getting Started

Generated from source roots:
- `Analyse`
- `Common`
- `FDTD`
- `cmake`
- `matlab`
- `nf2ff`
- `tools`
- `python/openEMS`

Use this map only after exhausting the topic docs in `doc_map.md`.

## Fast source navigation
- `rg -n "<symbol_or_keyword>" Analyse Common FDTD cmake matlab nf2ff tools python/openEMS`
- `rg -n "class|def|struct|namespace" Analyse Common FDTD cmake matlab nf2ff tools python/openEMS`
- `rg -n "SetBoundaryCond|SetCSX|Add.*Port|CalcPort|Run\\(|Write2XML|ParseFDTDSetup|SetupFDTD|RunFDTD" python/openEMS matlab openems.cpp openems.h main.cpp`
- If a doc mentions a function/class, search that exact symbol first, then inspect nearby implementation files.

## Priority function-level entry points
- `python/openEMS/openEMS.pyx` | `SetBoundaryCond()`, `SetCSX()`, `AddLumpedPort()`, `AddRectWaveGuidePort()`, `AddMSLPort()`, `Run()`, `Write2XML()`
- `python/openEMS/ports.py` | `MSLPort.__init__()` mesh/probe checks, `Port.CalcPort()`, `WaveguidePort.CalcPort()`
- `python/openEMS/nf2ff.py` | `CalcNF2FF()` runtime/postprocessing path when NF2FF enters starter workflows
- `matlab/SetBoundaryCond.m` | boundary-condition cardinality/type checks (`wrong number`, `unknown boundary`)
- `matlab/AddMSLPort.m` | mesh/probe/excitation placement errors and excitation setup
- `matlab/AddRectWaveGuidePort.m` | mode setup wrapper for rectangular waveguide ports
- `matlab/AddWaveGuidePort.m` | common waveguide port implementation for Matlab/Octave tutorials
- `matlab/InitFDTD.m` | starter defaults (`NrTS`, `EndCriteria`, coordinate mode)
- `matlab/RunOpenEMS.m` | option forwarding (`--no-simulation`, engine/debug flags) and execution handoff
- `openems.h` | solver API surface (`ParseFDTDSetup`, `SetupFDTD`, `RunFDTD`, `SetCSX`)
- `openems.cpp` | implementation of parse/setup/run pipeline and runtime warnings for invalid setup
- `main.cpp` | executable startup sequence (`ParseFDTDSetup` -> `SetupFDTD` -> `RunFDTD`)

## Symptom-driven starting points
- `Add*Port: CSX is not set!` -> `python/openEMS/openEMS.pyx` (`SetCSX()` and `Add*Port()` guards)
- `Invalid boundary condition size!` -> `python/openEMS/openEMS.pyx` and `matlab/SetBoundaryCond.m`
- MSL mesh/probe placement failures -> `python/openEMS/ports.py` (`MSLPort.__init__()`) and `matlab/AddMSLPort.m`
- CLI or `Run()` option mismatch -> `python/openEMS/openEMS.pyx` (`Run()`), `matlab/RunOpenEMS.m`, `openems.cpp`
