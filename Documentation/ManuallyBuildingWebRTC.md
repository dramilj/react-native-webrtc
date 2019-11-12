# Building WebRTC for Android

This document shows how to build WebRTC libraries needed by the Android part of this library.


## Install prerequisite tools

Clone the depot_tools repository which includes all required tools - custom git binary, ninja build tools, gclient, gn, fetch, etc.

```
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

Add path to depot_tools repository to PATH, so the binary included in the repository would be available in the terminal (command line).

```
export PATH=$PATH:/path/to/depot_tools
```


## Getting the code

Use fetch command to get the WebRTC source code for Android. Before doing this you may want to go over to another or new directory to place the source code in.

```
fetch --nohooks webrtc_android
```

After fetch, you need to sync gclient. Note: this process may take a long time as it performs a checkout of Android build chain tools (around 16 GB).

```
gclient sync
```

Install the dependencies needed to build the source code. Note: you need also OpenJDK installed.

```
./build/install-build-deps.sh
```

If you need to build different version of WebRTC library, you can switch to different branch or tag with following command:

```
git checkout branch_name (e.g. branch-heads/69)
```

## Compilation

### Using AAR build tools

```
tools_webrtc/android/build_aar.py
```

This command would compile the source code for all supported cpu type such as arm64-v8a, armeabi-v7a, x86, x86_64 and then package these native libraries and .jar library into .aar file. .AAR file resides in your current working directory or src/ directory with default name libwebrtc.aar.

### Manual compilation

Manually generate projects for each particular cpu type using GN:

```
gn gen out/Debug --args='target_os="android" target_cpu="x64"'
```

For release:

```
gn gen out/Release --args='is_debug=false is_component_build=false rtc_include_tests=false target_os="android" target_cpu="x64"'
```

The output is in the out/Debug or out/Release (directory name is arbitrary), target_cpu can be arm64, x86 or x64.

After one of these steps, you need to do compilation with ninja.

```
ninja -C out/Release
```

Compilation may take long depending on how powerful your machine. Output of compilation is in the out/Debug or out/Release directory, native .so file is in the lib.unstripped/, and the java .jar library is lib.java/ directory.

### Note for older branches

depot_tools are not tracked with your checkout, so it’s possible `gclient sync` will break on sufficiently old branches. In that case, you can try using an older depot_tools:

```
which gclient
# cd to depot_tools dir
# edit update_depot_tools; add an exit command at the top of the file
git log  # find a hash close to the date when the branch happened
git checkout <hash>
cd ~/dev/webrtc/src
gclient sync
# When done, go back to depot_tools, git reset --hard, run gclient again and
# verify the current branch becomes REMOTE:origin/master
```
