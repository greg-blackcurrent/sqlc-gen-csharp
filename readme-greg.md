# Building the wasm plugin locally 

## One-time setup

```bash
export DOTNET_ROOT="$HOME/.dotnet"
export PATH="$HOME/.dotnet:$PATH"

# WasmRunner targets net8.0, so an 8.0 SDK must be present in ~/.dotnet.
# (install once if missing)
wget -q https://dot.net/v1/dotnet-install.sh -O /tmp/dotnet-install.sh && chmod +x /tmp/dotnet-install.sh
/tmp/dotnet-install.sh --channel 8.0 --install-dir "$HOME/.dotnet"

# Pin global.json to 8.0 so the 8.0 SDK is selected (needed so the workload
# install pulls the net8-band wasi runtime pack, not the net10 one):
cat > global.json <<'EOF'
{
  "sdk": {
    "version": "8.0.400",
    "rollForward": "latestPatch"
  }
}
EOF

# Install the wasi workload under the 8.0 SDK 
dotnet workload install wasi-experimental

# Download + extract WASI SDK v22 (matches CI's WASI_SDK_VERSION: 22)
wget https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-22/wasi-sdk-22.0-linux.tar.gz
tar xf wasi-sdk-22.0-linux.tar.gz
```

## Build (every time)

```bash
export DOTNET_ROOT="$HOME/.dotnet"
export PATH="$HOME/.dotnet:$PATH"

WASI_SDK_PATH="$(pwd)/wasi-sdk-22.0" dotnet publish WasmRunner -c release

# The fresh wasm lands at (both are the same file):
cp WasmRunner/obj/release/net8.0/wasi-wasm/wasm/for-publish/WasmRunner.wasm dist/plugin.wasm
```


