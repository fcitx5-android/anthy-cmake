# anthy-cmake

Build [Anthy](https://github.com/fujiwarat/anthy-unicode) with CMake

## Usage

```sh
cmake -B build . -DCMAKE_INSTALL_PREFIX=./install
cmake --build build
cmake --build build --target install
```

## Build with Android NDK

TBD.

Try [build-android.sh](./build-android.sh)

