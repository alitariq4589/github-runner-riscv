<p align="center">
  <img src="docs/res/github-graph.png">
</p>

# RISC-V Port Docs

This `main_riscv` branch is patched for building and execution on native RISC-V.

## Set of changes

- The Nodejs is compiled using the official docs for building on Milk-V Pioneer Box and is set to release monthly with latest tag at [this repo](https://github.com/alitariq4589/nodejs-riscv) 

- The .NET version in the github actions runner source code is changed to version 8.0.101 (which can be cross-compiled for RISC-V using [dotnet-installer](https://github.com/dotnetinstaller/dotnetinstaller))
- Following .NET packages were not produced while cross-compiling .NET for RISC-V and were not used in building github runner package for `linux-riscv64`:
  - System.IO.FileSystem.AccessControl 
  - System.ServiceProcess.ServiceController
  - System.Runtime.Loader" Version
  - System.ServiceProcess.ServiceController
- While building the github actions runner package from this source code on RISC-V, .NET should be present inside the `_dotnetsdk` in the repository root and the `.nupkgs` produced with .NET SDK build should be present in `$HOME/packages` (see the build docs below) 
- Since .NET release is not available for RISC-V through official .NET servers, it is built separately and is placed inside the source code 

## Building on a RISC-V compute

This source can be built on native RISC-V compute (`qemu-system-riscv64` or a RISC-V board)

Change to the desired directory for build

Clone the repo:

```
git clone -b main_riscv https://github.com/alitariq4589/github-runner-riscv.git
```

Setup the directories:

```
mkdir -p ~/packages github-runner-riscv/_dotnetsdk/8.0.101
```

Fetch dotnet:

```
wget -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/dotnet-sdk-8.0.101-linux-riscv64.tar.gz
```

Fetch the nupkgs in the `$HOME/packages`:

```

wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.AspNetCore.App.Runtime.linux-riscv64.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.AspNetCore.App.Runtime.linux-riscv64.8.0.1.symbols.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Common.ItemTemplates.8.0.101.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Common.ItemTemplates.8.0.101.symbols.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Common.ProjectTemplates.8.0.8.0.101.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Common.ProjectTemplates.8.0.8.0.101.symbols.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Web.Client.ItemTemplates.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Web.ItemTemplates.8.0.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.DotNet.Web.ProjectTemplates.8.0.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.NETCore.App.Host.linux-riscv64.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.NETCore.App.Runtime.linux-riscv64.8.0.1.nupkg
wget -P ~/packages -q https://github.com/alitariq4589/dotnet-riscv/releases/download/v8.0.101/Microsoft.NETCore.App.Runtime.linux-riscv64.8.0.1.symbols.nupkg
```

Extract the dotnet to the source code directory:

```
tar -xf dotnet-sdk-8.0.101-linux-riscv64.tar.gz -C github-runner-riscv/_dotnetsdk/8.0.101
```

Build:

```
cd github-runner-riscv/src && ./dev.sh layout Release linux-riscv64
```

Package (this will produce a tarball which will contain binaries in `github-runner-riscv/_package`):

```
cd github-runner-riscv/src && ./dev.sh package Release linux-riscv64
```


# GitHub Actions Runner

[![Actions Status](https://github.com/actions/runner/workflows/Runner%20CI/badge.svg)](https://github.com/actions/runner/actions)

The runner is the application that runs a job from a GitHub Actions workflow. It is used by GitHub Actions in the [hosted virtual environments](https://github.com/actions/virtual-environments), or you can [self-host the runner](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/about-self-hosted-runners) in your own environment.

## Get Started

For more information about installing and using self-hosted runners, see [Adding self-hosted runners](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/adding-self-hosted-runners) and [Using self-hosted runners in a workflow](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/using-self-hosted-runners-in-a-workflow)

Runner releases:

![win](docs/res/win_sm.png) [Pre-reqs](docs/start/envwin.md) | [Download](https://github.com/actions/runner/releases)  

![macOS](docs/res/apple_sm.png)  [Pre-reqs](docs/start/envosx.md) | [Download](https://github.com/actions/runner/releases)  

![linux](docs/res/linux_sm.png)  [Pre-reqs](docs/start/envlinux.md) | [Download](https://github.com/actions/runner/releases)

### Note

Thank you for your interest in this GitHub repo, however, right now we are not taking contributions. 

We continue to focus our resources on strategic areas that help our customers be successful while making developers' lives easier. While GitHub Actions remains a key part of this vision, we are allocating resources towards other areas of Actions and are not taking contributions to this repository at this time. The GitHub public roadmap is the best place to follow along for any updates on features we’re working on and what stage they’re in.

We are taking the following steps to better direct requests related to GitHub Actions, including:

1. We will be directing questions and support requests to our [Community Discussions area](https://github.com/orgs/community/discussions/categories/actions)

2. High Priority bugs can be reported through Community Discussions or you can report these to our support team https://support.github.com/contact/bug-report.

3. Security Issues should be handled as per our [security.md](security.md)

We will still provide security updates for this project and fix major breaking changes during this time.

You are welcome to still raise bugs in this repo.
