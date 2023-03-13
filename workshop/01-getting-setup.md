# Getting Started

The first thing we'll need to do is get Bactopia installed, and for this we'll use Conda.

## Goals for this Section

- [ ] *(Optional)* Install MambaForge
- [ ] Create a Bactopia Environment
- [ ] Verify v3.0.0 of Bactopia is installed

## *(Optional)* Install MambaForge

If you do not have Conda installed I recommend installing
[MambaForge](https://github.com/conda-forge/miniforge#mambaforge), as it comes with Mamba
pre-installed. You'll want to follow the [MambaForge Install Instructions](https://github.com/conda-forge/miniforge#unix-like-platforms-mac-os--linux)
for this. This will take a few minutes, but once complete you'll be ready to install
Bactopia.

## Installation

At the time of writing this, Bactopia is undergoing a major version change (*v2 -> v3*). Because of
this inbound change, I felt it best to avoid using v2 for this workshop. So, we'll be installing 
a development build of Bactopia. This build includes a preview to version 3 of Bactopia. 

```bash
mamba create -n bactopia-dev -c rpetit3 -c conda-forge -c bioconda bactopia
```

If all goes well you should now have Bactopia v3 installed.

## Verify Version Installed

Let's go ahead and activate the new environment, and we'll make sure the correct version is installed.
For this we'll use `--version`:

```
conda activate bactopia-dev
bactopia --version
bactopia 3.0.0
```

If successful, you are ready to proceed to [Bactopia Subcommands](/workshop/02-bactopia-subcommands.md)!
