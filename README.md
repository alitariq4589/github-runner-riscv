# GitHub Actions Runner for RISC-V (linux-riscv64)

GitHub [actions/runner](https://github.com/actions/runner) with build support for RISC-V 64-bit Linux systems.

---

## Overview

This fork provides a self-hosted GitHub Actions runner compiled natively for `linux-riscv64` (until official maintainers start accepting RISC-V PRs). Because the official Actions team does not publish RISC-V binaries, this project patches the upstream source to produce a fully self-contained runner that can be deployed directly on RISC-V hardware or emulated environments.

The build pipeline:
- Uses .NET 10.0.102 SDK cross-compiled for RISC-V (by [@filipnavara](https://github.com/filipnavara/dotnet-riscv)). Because older .NET versions dont have support for RISC-V and throw errors and bugs after a certain time of connection (famous issues resolved by using .NET 10 is [this](https://github.com/actions/runner/issues/3981#issuecomment-3906578978) and [this](https://github.com/actions/runner/issues/3393)). Check [this](https://github.com/dotnet/runtime/issues/36748) issue on official .NET repo for RISC-V support in .NET
- Targets `net8.0` with patched SDK metadata to unlock AppHost generation for `linux-riscv64`
- Bundles RISC-V builds of [Node.js 20](https://unofficial-builds.nodejs.org/download/release/v20.9.0/) and [Node.js 24](https://github.com/alitariq4589/nodejs-riscv/releases/) for action execution
- Disables the runner's built-in auto-updater (no official RISC-V packages exist upstream to update to)
- Built on Milk-V Pioneer Box and Debian Trixie in native RISC-V architecture

All the changes are added in the worklfow. Check [this](https://github.com/alitariq4589/github-runner-riscv/blob/riscv_dotnet10_build/.github/workflows/build_riscv.yaml) workflow for details on how this package is built.

---

## Installation

### 1. Download the latest release

Download the latest release from [Releases](https://github.com/alitariq4589/github-runner-riscv/releases)

### 2. Extract

```bash
mkdir actions-runner && cd actions-runner
tar xzf ../actions-runner-linux-riscv64-X.X.X.tar.gz
```

### 4. Configure

```bash
./config.sh --url https://github.com/YOUR-ORG/YOUR-REPO --token YOUR-RUNNER-TOKEN
```

You can generate a runner token from **Settings → Actions → Runners → New self-hosted runner** in your GitHub repository or organization.

### 5. Run

```bash
# Interactive (foreground)
./run.sh

# As a systemd service (recommended for production)
sudo ./svc.sh install
sudo ./svc.sh start
```

---

## System Requirements

| Requirement | Details |
|---|---|
| CPU | RISC-V 64-bit (RV64GC or compatible) |
| OS | Linux (Ubuntu 22.04+ or Debian 12+ recommended) |
| Disk | ~500 MB for the runner + job workspace |
| .NET runtime | Bundled — no separate installation required |
| Node.js | Bundled (v20 and v24) — no separate installation required |

---



## Build Details

This project is built entirely on a native `RISC-V 64` GitHub Actions runner (self-hosted). The workflow performs the following steps:

**SDK Setup**
- Downloads `.NET 10.0.102` for `linux-riscv64` from filipnavara's unofficial RISC-V .NET port.
- Synthesizes `net8.0` runtime packs (`Microsoft.NETCore.App`, `Microsoft.AspNetCore.App`, and `Microsoft.NETCore.App.Host`) by copying the `net10.0` packs and registering them under version `8.0.23`.
- Patches `Microsoft.NETCoreSdk.BundledVersions.props` to register `linux-riscv64` and `linux-musl-riscv64` as valid AppHost and RuntimePack targets for `net8.0`.

**Source Patches**
- `dev.sh` — adds `riscv64` architecture detection and updates the SDK version reference.
- `SelfUpdater.cs` / `SelfUpdaterV2.cs` — disables the auto-update logic entirely, since no `linux-riscv64` packages exist on the official GitHub releases page. Without this patch, the runner would crash or cancel the running jobs attempting to self-update.
- `VarUtil.cs` — injects a runtime check using `RuntimeInformation.ProcessArchitecture` to correctly identify the RISC-V architecture and return `"RISCV64"` before the existing switch statement runs.
- In all `.csproj` files, `linux-riscv64` is appended to the `<RuntimeIdentifiers>` list.

**Node.js Bundling**
- Node.js 20 (`v20.9.0`) is sourced from [unofficial-builds.nodejs.org](https://unofficial-builds.nodejs.org).
- Node.js 24 (`v24.7.0`) is sourced from [@alitariq4589's nodejs-riscv releases](https://github.com/alitariq4589/nodejs-riscv).

**Output**
- A self-contained `actions-runner-linux-riscv64-{version}.tar.gz` package published as a GitHub Release and build artifact.

---

## Known Limitations

- **No auto-update.** The runner's self-update mechanism is permanently disabled. To update, download and redeploy a new release from this repository.
- **Unofficial .NET runtime.** The bundled .NET runtime is not an official Microsoft build (even though there are no changes added to the official source code). It is provided by the community RISC-V .NET port maintained by [@filipnavara](https://github.com/filipnavara/dotnet-riscv).
- **`net8.0` target on a `net10.0` SDK.** The runtime packs are copied from `net10.0` and relabeled as `net8.0`. Behavioral differences between these runtimes are possible but have not been observed to affect runner functionality in testing.

---


## Credits

| Project | Link |
|---|---|
| GitHub Actions Runner (upstream) | https://github.com/actions/runner |
| .NET for RISC-V (filipnavara), Many Thanks :) | https://github.com/filipnavara/dotnet-riscv |
| Node.js unofficial RISC-V builds | https://unofficial-builds.nodejs.org |
| Node.js 24 for RISC-V (alitariq4589) | https://github.com/alitariq4589/nodejs-riscv |

---

## License

This project inherits the [MIT License](LICENSE) from the upstream `actions/runner` repository. All patches and additions in this fork are provided under the same license.

## Contributions

Found an issue in this build? I will be happy to discuss. Create an issue in this repository or join the [Cloud-V's](https://cloud-v.co/) RISC-V community [Discord server](https://discord.gg/H7EGrzV93p) for engaging with us.