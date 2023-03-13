## Getting Started

The first thing we'll need to do is get Bactopia installed, and for this we'll use Conda. If
you do not have Conda installed I recommend installing
[MambaForge](https://github.com/conda-forge/miniforge#mambaforge), as it comes with Mamba
pre-installed.

### Installation

For this workshop we'll be using a development build of Bactopia. This build includes a preview
to version 3 of Bactopia. For an introduction into Bactopia, its going to be best to start with
version 3. So, let's get v3 installed:

```bash
mamba create -n bactopia-dev -c rpetit3 -c conda-forge -c bioconda bactopia
conda activate bactopia-dev
bactopia --version
bactopia 3.0.0
```

If all goes well you should now have Bactopia v3 installed and are ready to get started!
