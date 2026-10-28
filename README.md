# Flowpilot stand alone

<!-- fork-branch-note:start -->
> [!IMPORTANT]
> **Branch `flowpilot-city-g7-brazil` -- Honda City (Brazil only) 2023 (G7) on flowpilot (standalone APK) + black panda.**
> Base: a snapshot of [jagheterfredrik/flowpilot](https://github.com/jagheterfredrik/flowpilot) master `9ea918ea`
> (2025-03-04; standalone APK, car layer ~ openpilot 0.9.2-0.9.4, panda flattened at commaai/panda `abdc4185`).
> The snapshot was imported as a tarball, so this repo has no upstream history. Target device: Xiaomi Poco F3 +
> STM32F4 black panda. **Not tested on phone, panda or car.** The APK is built by the Build GitHub Actions workflow
> (artifact `android-release.apk`; first complete APK: run 40697331404 at `ffd228c`. The earlier run 40683947737 / `d079568`
> produced an APK without the Python tree and without the native libs, so it hung at "Booting Flowpilot").
>
> **What is new / modified on this branch (car port: 3 commits on top of the snapshot `43eab22`; then build/CI fixes):**
>
> | Commit | Files | Change |
> |---|---|---|
> | `a2f1983` | `selfdrive/car/honda/{values,interface,carstate,fingerprints}.py`, `opendbc/generator/honda`, `opendbc/*.dbc`, `docs/CARS.md`, routes | `HONDA_CITY_7G` radarless Bosch platform (harness Bosch B), FW + CAN fingerprints (36 msgs checked against a real City route), `STEER_THRESHOLD` 600, `HAS_EPB` auto-detection (City has no `0x1C2 EPB_STATUS`), torque tune `[1.2, 1.2, 0.23]` + `ki` 0.3, `steerActuatorDelay` 0.15, CVT `S` gear accepted, DBC `BO_ 401 GEARBOX` with the upstream `GEARBOX_CVT` layout (`1 P 2 R 3 N 4 D 7 L 10 S`) |
> | `fd9e846` | `panda/` | Windows-only SCons helper to build the black panda firmware from this tree without Docker |
> | `275df51` | `selfdrive/car/*/interface.py`, `selfdrive/car/honda/values.py`, `docs/CARS.md` | fix pre-existing `CarInterface.apply(c, sm, now_nanos)` signature mismatch with `controlsd` in 10 brands; Honda `STEER_*` constants; regenerated `CARS.md` |
> | `77a4fd7` | `*.sh` | restore the executable bit lost in the tarball import |
> | `ee6d526` | `.gitmodules`, 6 gitlinks | restore the submodule gitlinks (capnproto-java, laika, rednose, eigen3, libusb, libzmq) pinned to the upstream SHAs |
> | `660a9c2` | `flowy/recipes/acados`, `.github/workflows/build.yml` | tolerate clang 19 implicit-declaration errors in qpOASES; fail CI when no APK is produced |
> | `137e262` | `flowy/buildozer.spec` | pin `p4a.commit = b0efb9f3` (p4a develop, 2025-03) and `android.ndk = 25b` (the NDK hardcoded in `SConstruct`/`scripts/build.sh`) |
> | `d079568` | `flowy/pip-constraints.txt`, `scripts/build.sh` | `PIP_CONSTRAINT` pinning `build==1.2.2` / `virtualenv==20.29.3`: `build>=1.4.3` strips `PYTHONPATH` and breaks p4a's hostpython (numpy/mesonpy `_posixsubprocess`) |
> | `41801d8` | 16 symlinks, `panda/board/obj/.placeholder`, `panda/tests/misra/suppressions.txt`, `third_party/CL/libOpenCL.so`, `docker/Dockerfile`, `.github/workflows/build.yml` | restore the symlinks (`flowy/src/{cereal,common,opendbc,panda,rednose,selfdrive,system}`, `laika`, `rednose`, ...) and git-ignored files dropped by the tarball import, so scons builds and p4a packages the Python tree; CI now also checks the native libs |
> | `973e185` | `docker/Dockerfile` | pin `cython==3.0.12` / `numpy==2.2.3` in the build venv (buildozer 1.6.0 pulls `cython<3`, which the numpy 2 Cython headers reject) |
> | `ffd228c` | 164 files | restore the executable bit on the remaining scripts/binaries (`panda/crypto/sign.py`, `third_party/acados/*/t_renderer`, ...) |
>
> Panda firmware: built from this tree (`panda.bin.signed`, version `DEV-a2f1983c-DEBUG`, debug-signed -> the stock
> release bootstub must be replaced once over DFU). `safety_honda.h` is unchanged (`HONDA_BOSCH` + `RADARLESS`),
> `tests/safety/test_honda.py` 209/209 on a mingw-w64 build. Scope research, port report (PT-BR), firmware binaries,
> Poco F3 install and step-by-step flash procedure:
> [kriptomad/PandaH7Openpilot/10_FLOWPILOT](https://github.com/kriptomad/PandaH7Openpilot/tree/main/10_FLOWPILOT).
> Sibling ports of the same car: [opendbc@firmwarecityg7-v0.9.9](https://github.com/kriptomad/opendbc/tree/firmwarecityg7-v0.9.9),
> [openpilot@v0.9.9-cityg7-blackpanda](https://github.com/kriptomad/openpilot/tree/v0.9.9-cityg7-blackpanda).
<!-- fork-branch-note:end -->
This is a fork of Flowpilot without the need of rooting or a Termux environment. Install the apk and you're done. Requires a Snapdragon based Android phone.

Main features of this fork:
* Standalone apk without additional software requirements
* Android device rooting is not required
* Improved UI resolution

Huge shout out to the original [Flowpilot](https://github.com/flowdriveai/flowpilot) and [Phr00t's fork](https://github.com/phr00t/flowpilot).

# Building notes
Clone:
* `git clone --single-branch --depth 1 https://github.com/jagheterfredrik/flowpilot.git`
* `cd flowpilot`
* `git submodule update --init --recursive`

Setup the environment once:
* Install Docker with [non-root management](https://docs.docker.com/engine/install/linux-postinstall/)
* `./build_env.sh`

Then build using
* `./build.sh full`

One can also build only subcomponents:
* To package the Python code:
  * `./build.sh flowy`
* To build only binary requirements:
  * `./build.sh scons`
* To package the Android app:
  * `./build.sh app`

# Community

[<img src="https://assets-global.website-files.com/6257adef93867e50d84d30e2/636e0b5061df29d55a92d945_full_logo_blurple_RGB.svg" width="200">](https://discord.com/invite/APJaQR9nhz)

Flowpilot's core community lives on the official flowdrive [discord server](https://discord.com/invite/APJaQR9nhz). Check the pinned messages or search history through messages to see if your issues or question has been discussed earlier. You may also join [more awesome](https://linktr.ee/flowdrive) openpilot discord communities. 

We also push frequent updates on our [twitter handle](https://twitter.com/flowdrive_ai).

# Disclaimer 

THIS IS ALPHA QUALITY SOFTWARE FOR RESEARCH PURPOSES ONLY. THIS IS NOT A PRODUCT. YOU ARE RESPONSIBLE FOR COMPLYING WITH LOCAL LAWS AND REGULATIONS. NO WARRANTY EXPRESSED OR IMPLIED.
