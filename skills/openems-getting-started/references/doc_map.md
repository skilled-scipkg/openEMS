# openems documentation map: Getting Started

Generated from documentation and runnable-example roots:
- `python/doc`
- `matlab/Tutorials`
- `python/Tutorials`
- `.github/smoketests`
- `python/Tests`
- `TESTSUITE`

Use this map for first simulation startup and basic validation before inspecting source.

## Core onboarding docs
- `README` | package scope and layout
- `INSTALL` | C++ dependency/build prerequisites
- `python/README.md` | Python install flow, env vars, verification rules
- `python/doc/index.rst` | Python doc root
- `python/doc/openEMS.rst` | `openEMS` object overview
- `python/doc/openEMS_API.rst` | API entry points
- `python/doc/Tutorials/Intro_Tutorials.rst` | tutorial navigation

## Tutorial docs (simulation baselines)
- `python/doc/Tutorials/Rect_Waveguide.rst` | rectangular waveguide walkthrough
- `python/doc/Tutorials/MSL_NotchFilter.rst` | microstrip notch filter walkthrough
- `python/doc/Tutorials/Simple_Patch_Antenna.rst` | simple patch starter
- `python/doc/Tutorials/Helical_Antenna.rst` | helical antenna setup
- `python/doc/Tutorials/RCS_Sphere.rst` | RCS baseline
- `python/doc/Tutorials/CRLH_Extraction.rst` | CRLH extraction baseline
- `python/doc/Tutorials/Bent_Patch_Antenna.rst` | bent patch workflow

## Runnable smoke and tutorial scripts
- `.github/smoketests/python/MSL_NotchFilter.py` | fastest Python end-to-end run (`NrTS=1000`)
- `.github/smoketests/octave/MSL_NotchFilter.m` | fastest Octave end-to-end run (`NrTS=1000`)
- `python/Tutorials/Rect_Waveguide.py` | first Python tutorial run with `CalcPort`
- `python/Tutorials/MSL_NotchFilter.py` | Python notch-filter tutorial baseline
- `matlab/Tutorials/Rect_Waveguide.m` | first Matlab/Octave tutorial run
- `matlab/Tutorials/MSL_NotchFilter.m` | Matlab/Octave notch-filter baseline

## Regression-oriented checks
- `python/Tests/MSL_With_Local_Absorbers.py` | local absorber behavior check
- `python/Tests/Rect_Waveguide_W_Local_Absorbers.py` | waveguide + local absorber behavior check
- `TESTSUITE/run_testsuite.m` | broader Matlab/Octave regression harness
