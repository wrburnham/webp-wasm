This is a bare-bones webp / wasm test. There are several other examples around, this draws from them.

I tried this compile instruction but it stripped malloc and free out of the build as it was too aggressive with optimizations.

```
emcc -O3 -s WASM=1 -s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap"]' -I ../libwebp libwebp-wasm.c ../libwebp/src/{dec,dsp,demux,enc,mux,utils}/*.c
```

Toning down the optimizations a bit and adding `ALLOW_MEMORY_GROWTH` I was able to get the test to work (create a webp image from a non webp image).

```
emcc -O1 -s WASM=1 -s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap"]' -s ALLOW_MEMORY_GROWTH=1 -I ../libwebp libwebp-wasm.c ../libwebp/src/{dec,dsp,demux,enc,mux,utils}/*.c
```

This assumes that the libwebp source is installed on the machine (in my case, in the `../libwebp` directory and aslo that `emcc` is installed for compilation.

I then compiled modularly

```
emcc -O1 -s WASM=1 -s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap"]' -s MODULARIZE=1 -s 'EXPORT_NAME="WebP"' -s ALLOW_MEMORY_GROWTH=1 -I ../libwebp libwebp-wasm.c ../libwebp/src/{dec,dsp,demux,enc,mux,utils}/*.c
```

testing compile for web with the goal of embedding in a react project 

```
emcc -O1 -s ENVIRONMENT=web -o webp.js -s WASM=1 -s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap"]' -s MODULARIZE=1 -s 'EXPORT_NAME="WebP"' -s ALLOW_MEMORY_GROWTH=1 -I ../libwebp libwebp-wasm.c ../libwebp/src/{dec,dsp,demux,enc,mux,utils}/*.c
```

For an example of this module at work, see [here](https://wrburnham.github.io/etc/libwebp-encode/).

