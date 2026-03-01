---
name: openems-api-and-scripting
description: This skill should be used when users ask about api and scripting in openems; it prioritizes documentation references and then source inspection only for unresolved details.
---

# openems: API and Scripting

## High-Signal Playbook

### Route conditions
- Use this skill for C++ build/install flow, Python binding build/install flow, and API-level scripting/debugging questions.
- Route simulation modeling and first-tutorial questions to `openems-getting-started`.
- Escalate from docs to source in this order: `CMakeLists.txt` and `cmake/Modules/FindTinyXML.cmake`, then `python/setup.py` and `python/bootstrap/find_package.py`, then runtime behavior in `python/openEMS/openEMS.pyx` and core solver files `openems.cpp`/`openems.h`.
- Primary anchors: `INSTALL`, `python/README.md`, `.github/workflows/ci.yml`, `python/doc/index.rst`, `python/doc/openEMS_API.rst`.

### Triage questions
- Are you building only C++ openEMS, only Python bindings, or both?
- Where are `fparser`, `CSXCAD`, and openEMS installed (single prefix or split)?
- Which CMake flags are set (`CMAKE_INSTALL_PREFIX`, `FPARSER_ROOT_DIR`, `CSXCAD_ROOT_DIR`)?
- Are `CSXCAD_INSTALL_PATH` and `OPENEMS_INSTALL_PATH` exported before `pip install .`?
- Which pip mode is used (`--no-build-isolation` vs default), and what pip major version?
- Is the error compile-time, link-time, import-time, or runtime option/CLI behavior?
- Are you inside a venv, and are you mixing `--user` with venv installs?
- Is the repository clone shallow (no tags), causing version-tag build issues?

### Canonical workflow
1. Build/install dependencies first (`fparser`, `CSXCAD`) to a known prefix.
2. Configure openEMS C++ with explicit roots (`-DCMAKE_INSTALL_PREFIX`, `-DFPARSER_ROOT_DIR`, `-DCSXCAD_ROOT_DIR` if needed).
3. Build and install C++ openEMS (`openEMS` shared lib + `openEMS` binary).
4. Export `CSXCAD_INSTALL_PATH` and `OPENEMS_INSTALL_PATH` (same prefix recommended).
5. Install Python package from `python/` with `pip install .` (or `--no-build-isolation` on constrained/older environments).
6. On old pip workflows where CSXCAD auto-detection fails, set `CSXCAD_PYSRC_PATH` explicitly.
7. Verify imports and version from a neutral directory.
8. Validate behavior with smoke scripts and then an API-heavy tutorial.
9. If unresolved, trace through `python/setup.py`/bootstrap scripts and C++ option parsing in `openems.cpp`.

### Minimal working example
C++ build/install baseline (from this repository, with dependencies already installed in one prefix):
```bash
mkdir -p build
cd build
cmake .. \
  -DCMAKE_INSTALL_PREFIX=$HOME/opt/openems \
  -DFPARSER_ROOT_DIR=$HOME/opt/openems \
  -DCSXCAD_ROOT_DIR=$HOME/opt/openems
cmake --build . -j"$(nproc)"
cmake --install .
cd ..
```

Python binding install + verification:
```bash
cd python
export CSXCAD_INSTALL_PATH=$HOME/opt/openems
export OPENEMS_INSTALL_PATH=$HOME/opt/openems
pip3 install . --verbose
cd /tmp
python3 -c "import CSXCAD, openEMS; print(CSXCAD.__version__, openEMS.__version__)"
cd -
python3 .github/smoketests/python/MSL_NotchFilter.py
```

### Pitfalls and fixes
- CMake cannot find dependency libs/headers: pass explicit root hints (`FPARSER_ROOT_DIR`, `CSXCAD_ROOT_DIR`, optional `TinyXML_ROOT_DIR`) and reconfigure (`CMakeLists.txt`, `cmake/Modules/FindTinyXML.cmake`).
- `RuntimeError` for missing install path env vars during Python build: set `OPENEMS_INSTALL_PATH` (and `CSXCAD_INSTALL_PATH`) or a matching `VIRTUAL_ENV` (`python/setup.py`, `python/README.md`).
- Old pip/out-of-tree build cannot locate CSXCAD Python source: set `CSXCAD_PYSRC_PATH`, use `--no-build-isolation`, or upgrade pip (`python/bootstrap/find_package.py`).
- Importing from `python/` causes local-shadow issues: run import checks outside source tree (`python/README.md`).
- Mixing `--user` with active venv can break isolation: avoid `--user` in venv (`python/README.md`).
- `setuptools` missing when no-build-isolation is used: install `setuptools`/`cython` first (`python/bootstrap/setuptools_build_meta_custom.py`, `python/pyproject.toml`).
- Version-tag build failures from shallow clones (`git describe` issues): use full clone with tags (`.github/workflows/ci.yml` comments around checkout depth).
- Manual compiler/linker overrides without path vars: use `*_INSTALL_PATH_IGNORE=1` plus explicit `CXXFLAGS`/`LDFLAGS` (`python/README.md` advanced install section).

### Convergence and validation checks
- Prefix integrity: ensure `OPENEMS_INSTALL_PATH` points to a prefix containing openEMS headers under `include` and libraries under `lib`.
- Binary readiness: run `openEMS --help` (or `openEMS.sh --help`) to confirm CLI parse path is healthy.
- Python readiness: from neutral directory, `import openEMS` and print `openEMS.__version__`.
- Functional smoke: run `.github/smoketests/python/MSL_NotchFilter.py` and at least one Octave smoke/tutorial.
- Behavior parity: if API flags differ from CLI expectations, compare Python `Run(..., **kw)` option mapping in `python/openEMS/openEMS.pyx` against `openems.cpp` option descriptors.

## Scope
- Handle questions about language bindings, APIs, and programmatic interfaces.
- Keep responses abstract and architectural for large codebases; avoid exhaustive per-function documentation unless requested.

## Primary documentation references
- `INSTALL`
- `python/README.md`
- `.github/workflows/ci.yml`
- `python/doc/index.rst`
- `python/doc/Tutorials/index.rst`
- `python/doc/ports.rst`
- `python/doc/nf2ff.rst`
- `python/doc/openEMS_API.rst`
- `python/doc/openEMS.rst`
- `python/doc/Tutorials/MicroWave_Tutorials.rst`
- `python/doc/Tutorials/Antenna_Tutorials.rst`

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
- `CMakeLists.txt`
- `cmake/Modules/FindTinyXML.cmake`
- `openems.h`
- `openems.cpp`
- `openems_global.h`
- `main.cpp`
- `python/pyproject.toml`
- `python/setup.py`
- `python/bootstrap/find_package.py`
- `python/bootstrap/setuptools_build_meta_custom.py`
- `python/openEMS/openEMS.pyx`
- `python/openEMS/openEMS.pxd`
- `python/openEMS/ports.py`
- `python/openEMS/nf2ff.py`
- `python/openEMS/_nf2ff.pyx`
- `python/openEMS/_nf2ff.pxd`
- `python/openEMS/__init__.py`
- `python/openEMS/utilities.py`
- `python/openEMS/physical_constants.py`
- `python/openEMS/automesh.py`
- Prefer targeted source search (for example: `rg -n "<symbol_or_keyword>" Analyse Common FDTD cmake matlab nf2ff tools python/openEMS`).
