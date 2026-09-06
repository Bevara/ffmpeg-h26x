# ffmpeg-h26x
This filter decodes ITU-T H.261 and H.263 video using a reduced version of ffmpeg carrying those two decoders only.

## Requirements

[CMake](https://cmake.org/) is used as a build system. To install it, follow
[Debian build instructions](developing_in_debian.md).

[Emscripten SDK](https://emscripten.org/) is required for building
WebAssembly artifacts. To install it, follow the
[Download and Install](https://emscripten.org/docs/getting_started/downloads.html)
guide:

```bash
cd $OPT

# Get the emsdk repo.
git clone https://github.com/emscripten-core/emsdk.git

# Enter that directory.
cd emsdk

# Download and install the latest SDK tools.
./emsdk install latest

# Make the "latest" SDK "active" for the current user. (writes ~/.emscripten file)
./emsdk activate latest
```

## Building the accessor

```bash
# Setup EMSDK and other environment variables. In practice EMSDK is set to be
# $OPT/emsdk.
source $OPT/emsdk/emsdk_env.sh

# Assuming you are in the root level of the cloned repo :
emcmake cmake .
emmake make
```

Once built, you can use and distribute ffmpeg-h26x_1.wasm with your universal tags.

## Rebuilding the ffmpeg libraries

`lib/` holds a static ffmpeg configured with nothing but the two decoders this
filter needs, which is what keeps the accessor under a megabyte:

```bash
emconfigure $FFMPEG_SRC/configure --target-os=none --arch=x86_32 \
    --enable-cross-compile --disable-x86asm --disable-inline-asm \
    --disable-stripping --disable-programs --disable-doc \
    --disable-runtime-cpudetect --disable-autodetect --disable-pthreads \
    --pkg-config-flags="--static" --nm="$EMSDK/upstream/bin/llvm-nm" \
    --ar=emar --ranlib=emranlib --cc=emcc --cxx=em++ --objcc=emcc --dep-cc=emcc \
    --enable-pic --disable-everything \
    --enable-decoder=h261 --enable-decoder=h263 --enable-decoder=h263i --enable-decoder=h263p
emmake make
```

## Notes

H.261 has no codec identifier in GPAC - it predates every container GPAC
muxes - so its four character code is used directly as the pid codec id, and
`avidmx` tags H.261 tracks with the same value.

`rfh263` is shipped alongside the decoder, so a bare `.263` elementary stream
plays without a demultiplexer. H.263 carries a temporal reference but no frame
rate, so a raw stream falls back to the 15 fps of the recommendation; an AVI or
3GP source gives the real cadence.

## Documentation

For more details, please visit our documentation at https://bevara.com/documentation/develop/.
