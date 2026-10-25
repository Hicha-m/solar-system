# OpenGL Space Cinematic

[![CI](https://github.com/Hicha-m/solar-system/actions/workflows/cmake-multi-platform.yml/badge.svg?branch=master)](https://github.com/Hicha-m/solar-system/actions/workflows/cmake-multi-platform.yml)
[![C++17](https://img.shields.io/badge/C%2B%2B-17-blue)](CMakeLists.txt)
[![OpenGL 3.3](https://img.shields.io/badge/OpenGL-3.3-5586a4)](#requirements)
[![Platforms](https://img.shields.io/badge/platforms-Linux%20%7C%20macOS%20%7C%20Windows-555)](#downloads)
[![Latest release](https://img.shields.io/github/v/release/Hicha-m/solar-system)](https://github.com/Hicha-m/solar-system/releases/latest)

A real-time space cinematic written in C++17 with OpenGL 3.3. The sequence
combines meteor impacts, Earth destruction, a shuttle flight and a camera
journey through the Solar System and the Milky Way.

![Space cinematic preview](image.gif)

## Downloads

Download the packaged application from [Release 1.0.2](https://github.com/Hicha-m/solar-system/releases/tag/1.0.2):

| Platform | Architecture | Download |
| --- | --- | --- |
| Linux | x64 (Intel / AMD) | [Download `.tar.gz`](https://github.com/Hicha-m/solar-system/releases/download/1.0.2/OpenGLSpaceCinematic-1.0.0-Linux-x86_64.tar.gz) |
| Linux | ARM64 | [Download `.tar.gz`](https://github.com/Hicha-m/solar-system/releases/download/1.0.2/OpenGLSpaceCinematic-1.0.0-Linux-aarch64.tar.gz) |
| macOS | Apple Silicon | [Download `.zip`](https://github.com/Hicha-m/solar-system/releases/download/1.0.2/OpenGLSpaceCinematic-1.0.0-Darwin-arm64.zip) |
| macOS | Intel | [Download `.zip`](https://github.com/Hicha-m/solar-system/releases/download/1.0.2/OpenGLSpaceCinematic-1.0.0-Darwin-x86_64.zip) |
| Windows | x64 (Intel / AMD) | [Download `.zip`](https://github.com/Hicha-m/solar-system/releases/download/1.0.2/OpenGLSpaceCinematic-1.0.0-Windows-AMD64.zip) |

Extract the complete archive before launching `project` on Linux, `project.app`
on macOS, or `project.exe` on Windows. Keep all bundled resources in place.
A graphics driver supporting OpenGL 3.3 is required.

See [all releases](https://github.com/Hicha-m/solar-system/releases) for other versions.

## Video

[Watch the project video on YouTube](https://youtu.be/fuUNtIMqqgY).

## Features

- Scripted camera animation with pause, restart, seeking and playback speed controls.
- Meteor showers, collision detection, particle trails and impact lights.
- Earth surface damage, heat, cracks and animated fragmentation.
- Textured planets, Saturn rings, HDR rendering and bloom.
- Music and impact sound playback through SDL3.
- Free camera mode for exploring the scene.
- MP4 export with configurable resolution, frame rate and audio.

## Requirements

To build from source:

| Dependency | Requirement |
| --- | --- |
| Compiler | C++17 support; C and C++ toolchains are required |
| CMake | 3.21 or newer |
| OpenGL | Driver supporting OpenGL 3.3 core |
| GLFW | 3.3 or newer |
| GLM | Development headers and CMake package |
| SDL3 | 3.2 or newer |
| FFmpeg | Available as `ffmpeg` on `PATH` |
| Python 3 | Required for MP4 export and the Python tests |

GLAD, stb_image and dr_mp3 are included in [`third_party/`](third_party/README.md).
FFmpeg prepares the impact sound during compilation and encodes video exports.
Normal application playback does not require FFmpeg.

Interactive playback requires a graphical desktop session. Run all build
commands below from the repository root unless another directory is specified.

## Build from source

```bash
git clone https://github.com/Hicha-m/solar-system.git
cd solar-system
```

### Linux

On Fedora, install the development dependencies:

```bash
sudo dnf install gcc gcc-c++ cmake make pkgconf-pkg-config \
  mesa-libGL-devel glfw-devel glm-devel SDL3-devel ffmpeg python3
```

On Ubuntu 24.04, install the system dependencies, then build SDL3 separately:

```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake ninja-build pkg-config \
  curl ca-certificates ffmpeg python3 libgl1-mesa-dev libglfw3-dev libglm-dev \
  libasound2-dev libpulse-dev libx11-dev libxext-dev libxrandr-dev \
  libxrender-dev libxfixes-dev libxi-dev libxss-dev libxtst-dev

mkdir -p build/deps
curl -fL https://github.com/libsdl-org/SDL/releases/download/release-3.2.10/SDL3-3.2.10.tar.gz \
  -o build/deps/SDL3-3.2.10.tar.gz
tar -xzf build/deps/SDL3-3.2.10.tar.gz -C build/deps
cmake -S build/deps/SDL3-3.2.10 -B build/deps/sdl-build -G Ninja \
  -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="$PWD/build/deps/sdl" \
  -DSDL_SHARED=ON -DSDL_STATIC=OFF -DSDL_TESTS=OFF -DSDL_TEST_LIBRARY=OFF \
  -DSDL_X11=ON -DSDL_WAYLAND=OFF
cmake --build build/deps/sdl-build --parallel 2
cmake --install build/deps/sdl-build
```

Configure the application. On Fedora:

```bash
cmake -S . -B build/cmake -DCMAKE_BUILD_TYPE=Release
```

On Ubuntu, point CMake to the SDL3 installation created above:

```bash
cmake -S . -B build/cmake -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_PREFIX_PATH="$PWD/build/deps/sdl"
```

Then build, run the unit tests and launch:

```bash
cmake --build build/cmake --parallel 2
ctest --test-dir build/cmake -L unit --output-on-failure
cmake --build build/cmake --target run
```

The Linux executable is `build/cmake/runtime/project`.

### macOS

With Homebrew installed:

```bash
brew install cmake glfw glm sdl3 ffmpeg python
cmake -S . -B build/cmake -DCMAKE_BUILD_TYPE=Release
cmake --build build/cmake --parallel 2
ctest --test-dir build/cmake -L unit --output-on-failure
cmake --build build/cmake --target run
```

The application bundle is `build/cmake/runtime/project.app`.

### Windows

Install Visual Studio 2022 with the **Desktop development with C++** workload,
CMake, Git and FFmpeg. Add FFmpeg to `PATH`.
Install Python 3 if you want to export videos or run the Python tests.

Clone and bootstrap vcpkg in a directory of your choice:

```powershell
git clone https://github.com/microsoft/vcpkg.git C:\dev\vcpkg
& C:\dev\vcpkg\bootstrap-vcpkg.bat
$env:VCPKG_ROOT = 'C:\dev\vcpkg'
```

Adjust this path if you already have vcpkg installed. From the project root,
run the following in PowerShell:

```powershell
cmake -S . -B build/cmake -G "Visual Studio 17 2022" -A x64 `
  "-DCMAKE_TOOLCHAIN_FILE=$env:VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake" `
  -DVCPKG_TARGET_TRIPLET=x64-windows-static `
  '-DCMAKE_MSVC_RUNTIME_LIBRARY=MultiThreaded$<$<CONFIG:Debug>:Debug>'
cmake --build build/cmake --config Release --parallel 2
ctest --test-dir build/cmake -C Release -L unit --output-on-failure
cmake --build build/cmake --config Release --target run
```

The manifest in [`vcpkg.json`](vcpkg.json) declares GLFW, GLM and SDL3 and pins
the vcpkg baseline. The executable is `build/cmake/runtime/project.exe`.

## Controls

| Key | Action |
| --- | --- |
| `Space` | Pause or resume the cinematic and music |
| `R` | Restart the cinematic |
| `Left` / `Right` | Seek backward / forward by 10 seconds |
| `Up` / `+` | Increase playback speed |
| `Down` / `-` | Decrease playback speed |
| `0` | Restore normal playback speed |
| `M` | Mute or unmute audio |
| `F3` | Switch between cinematic and free camera modes |
| `W` / `S` | Move forward / backward in free camera mode |
| `A` / `D` | Move left / right in free camera mode |
| `Z` / `X` | Move up / down in free camera mode |
| Mouse | Look around in free camera mode |
| `G` / `H` | Increase / decrease free camera movement speed |
| `F1` | Toggle wireframe rendering |
| `F2` | Toggle camera information |
| `F11` | Toggle fullscreen |
| `Esc` | Quit |

## Export to MP4

Build the application first. The exporter requires Python 3, FFmpeg with the
`libx264` and AAC encoders, and a working OpenGL context.

```bash
# Short preview
python3 tools/export_mp4.py --duration 5 --output exports/preview.mp4

# Full cinematic at 1080p and 60 fps
python3 tools/export_mp4.py --resolution 1080p --fps 60 --output exports/cinematic-1080p.mp4
```

On Windows, use `python` instead of `python3` if that is your Python command.
The default export is `exports/cinematic.mp4` at 1280 × 720 and 30 fps, with
H.264 video and AAC audio. Its duration follows the music track unless
`--duration` is supplied. Existing output files are never overwritten.

| Option | Description |
| --- | --- |
| `--resolution` | `720p`, `1080p`, `1440p`, `4k`, or even dimensions such as `1920x1080` |
| `--fps` | Frame rate from 1 to 240; default: 30 |
| `--duration` | Duration in seconds; default: the music duration |
| `--crf` | H.264 quality from 0 to 51; lower values increase quality; default: 18 |
| `--preset` | FFmpeg encoding preset; default: `medium` |
| `--no-audio` | Export without audio |
| `--output` | Destination MP4 file |
| `--executable` | Path to the application binary to use |

Run `python3 tools/export_mp4.py --help` for all options. For a build outside
`build/cmake`, pass its executable explicitly with `--executable`.

On Linux without a display, install Xvfb and xauth, then run the exporter under
Xvfb. For example:

```bash
LIBGL_ALWAYS_SOFTWARE=1 xvfb-run -a python3 tools/export_mp4.py \
  --duration 5 --output exports/headless-preview.mp4
```

## Tests

The unit tests cover assets, resource paths, audio, animation, meteor collisions,
particles and Earth destruction. The CMake build also registers the exporter
and CI metrics Python tests when a Python interpreter is available.

```bash
ctest --test-dir build/cmake -C Release -L unit --output-on-failure
```

The OpenGL integration suite is opt-in and requires a display. To enable it:

```bash
cmake -S . -B build/cmake -DENABLE_RUNTIME_TESTS=ON
cmake --build build/cmake --config Release --parallel 2
ctest --test-dir build/cmake -C Release -L runtime --output-on-failure
```

On Linux without a display, install Xvfb and xauth and replace the last command
with:

```bash
LIBGL_ALWAYS_SOFTWARE=1 xvfb-run -a ctest --test-dir build/cmake \
  -C Release -L runtime --output-on-failure
```

CTest configures a silent audio driver for automated tests. These tests check
audio initialization and playback logic; listening through speakers remains a
manual check.

## Packaging and releases

After a Release build, create a distributable archive:

```bash
cpack --config build/cmake/CPackConfig.cmake -C Release -B build/packages
```

CPack writes a `.tar.gz` archive on Linux and a `.zip` archive on Windows and
macOS. Extract the entire archive and keep its resources beside the executable,
or inside the macOS app bundle. Linux packages still require compatible system
libraries and graphics drivers. macOS bundles use ad-hoc signing.

The [GitHub Actions workflow](.github/workflows/cmake-multi-platform.yml) defines
builds for Linux x64 and ARM64, Windows x64, and macOS Apple Silicon and Intel.
Pushing a version tag such as `1.0.2` triggers the release workflow. Publication
requires all build jobs and the Docker job to succeed. Consult
[Actions](https://github.com/Hicha-m/solar-system/actions) for execution results
and [Releases](https://github.com/Hicha-m/solar-system/releases) for published
archives. A Git tag alone does not create a release.

## Docker

The [`Dockerfile`](Dockerfile) builds the application and SDL3 on Ubuntu 24.04.
During the image build, it runs the unit tests and the OpenGL integration suite
under Xvfb, then installs the application into the runtime image.

```bash
docker build -t solar-system .
```

The resulting image starts `./project` in `/app`. Interactive use requires
forwarding a display and audio server from the host; the build command above
performs the automated checks without that desktop setup.

## Project layout

```text
src/           Application, animation, audio, cameras, rendering and simulation
shaders/       GLSL vertex and fragment shaders
textures/      Planet and space textures
models/        Shuttle geometry and materials
audio/         Source music and impact sounds
third_party/   Bundled libraries and their license notices
tools/         Asset preparation and MP4 export
scripts/       CI, packaging verification and software renderer utilities
tests/         C++ and Python tests
docs/          CI and profiling documentation
```

See [`docs/ci.md`](docs/ci.md) for the CI configuration and
[`docs/profiling.md`](docs/profiling.md) for the optional profiling build.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| CMake cannot find SDL3 | Install the SDL3 development files. For the Ubuntu instructions above, include `-DCMAKE_PREFIX_PATH="$PWD/build/deps/sdl"` when configuring. |
| CMake cannot find FFmpeg | Check that `ffmpeg -version` works in the same terminal before configuring. |
| The application cannot create an OpenGL context | Check the graphics driver, OpenGL 3.3 support and access to a display. Use Xvfb for automated Linux runs without a display. |
| Resources are missing | Launch from the generated runtime directory or an extracted package. Keep the complete resource tree with the executable. |
| There is no sound | Check the system output and volume. Remove `SDL_AUDIO_DRIVER=dummy` from the environment for audible playback. |
| The exporter cannot find the executable | Build the project first or supply its path with `--executable`. |

## License

This repository does not currently include a project-level license. Third-party
library notices are documented in [`third_party/README.md`](third_party/README.md)
and its referenced license files.
