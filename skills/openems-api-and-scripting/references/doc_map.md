# openems documentation map: API and Scripting

Generated from build/runtime docs, CI configs, and verification scripts:
- `INSTALL`
- `python/README.md`
- `python/doc`
- `.github/workflows`
- `.github/smoketests`
- `python/Tests`

Use this map for build/install/runtime API troubleshooting before source deep dives.

## Build and packaging anchors
- `INSTALL` | C++ dependency list and baseline CMake invocation
- `python/README.md` | Python binding install modes and env vars
- `.github/workflows/ci.yml` | reproducible CI build/install sequence
- `python/pyproject.toml` | build backend and scm-version behavior

## API reference docs
- `python/doc/index.rst` | Python docs entry
- `python/doc/openEMS_API.rst` | Python API structure
- `python/doc/openEMS.rst` | `openEMS` class docs
- `python/doc/ports.rst` | port class behavior
- `python/doc/nf2ff.rst` | NF2FF API behavior

## API-focused tutorials
- `python/doc/Tutorials/index.rst` | tutorial index
- `python/doc/Tutorials/MicroWave_Tutorials.rst` | microwave tutorial index
- `python/doc/Tutorials/Antenna_Tutorials.rst` | antenna tutorial index

## Runtime verification scripts
- `.github/smoketests/python/MSL_NotchFilter.py` | quick Python runtime validation
- `.github/smoketests/octave/MSL_NotchFilter.m` | quick Octave runtime validation
- `python/Tests/MSL_With_Local_Absorbers.py` | local-absorber API behavior check
- `python/Tests/Rect_Waveguide_W_Local_Absorbers.py` | waveguide API behavior check
