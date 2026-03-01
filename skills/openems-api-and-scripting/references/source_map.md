# openems source map: API and Scripting

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
- `rg -n "determine_build_options|normalize_path_subdir|add_csxcad|pip_in_tree_build|get_requires_for_build_|Run\\(|_SetLibraryArguments|ParseFDTDSetup|SetupFDTD|RunFDTD" python cmake openems.cpp openems.h main.cpp`
- If a doc mentions a function/class, search that exact symbol first, then inspect nearby implementation files.

## Priority function-level entry points
- `CMakeLists.txt` | top-level dependency discovery, targets, install rules
- `cmake/Modules/FindTinyXML.cmake` | TinyXML hint variables and package lookup behavior
- `python/pyproject.toml` | build backend (`setuptools_build_meta_custom`) and scm-version settings
- `python/setup.py` | `normalize_path_subdir()`, `determine_build_options()`, `get_modules_list()`, extension wiring
- `python/bootstrap/find_package.py` | `is_project_root()`, `pip_in_tree_build()`, `add_csxcad()`, `add_h5py()`, `add_setuptool_scm()`
- `python/bootstrap/setuptools_build_meta_custom.py` | `get_requires_for_build_wheel()` and `get_requires_for_build_sdist()`
- `python/openEMS/openEMS.pyx` | `_SetLibraryArguments()` and `Run()` option mapping from Python to solver args
- `python/openEMS/ports.py` | `Port.CalcPort()` and subclass behavior used by scripted API postprocessing
- `python/openEMS/nf2ff.py` | `CalcNF2FF(..., read_cached=...)` runtime/caching behavior
- `python/openEMS/_nf2ff.pyx` | Python-to-C++ NF2FF binding boundary
- `openems.h` | solver API declarations (`ParseFDTDSetup`, `SetupFDTD`, `RunFDTD`)
- `openems.cpp` | solver runtime option handling and execution flow
- `main.cpp` | CLI entrypoint pipeline and fatal-path handling

## Symptom-driven starting points
- Missing include/lib paths during build -> `python/setup.py` (`determine_build_options()`) and `CMakeLists.txt`
- `RuntimeError` about install prefixes -> `python/setup.py` (`OPENEMS_INSTALL_PATH` checks)
- Old pip / CSXCAD source detection failures -> `python/bootstrap/find_package.py` (`pip_in_tree_build()`, `add_csxcad()`)
- `setuptools`/build-backend issues -> `python/bootstrap/setuptools_build_meta_custom.py` and `python/pyproject.toml`
- Python `Run(..., **kw)` behavior mismatch with CLI -> compare `python/openEMS/openEMS.pyx` and `openems.cpp`
