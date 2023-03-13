# Bactopia Workshop

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

## Exploring Bactopia Subcommands

The Bactopia subcommands are *helper* commands that can improve your usage Bactopia.
Each of these subcommands has recently been rewritten and made available from a Python
package called [bactopia-py](https://github.com/bactopia/bactopia-py). 

### `bactopia citations`

A quick way to get the citation information for all tools used by Bactopia. 

#### Example Citation

```{bash}
bactopia citations --name abricate
Abricate
Seemann T Abricate: mass screening of contigs for antimicrobial and virulence genes (GitHub)

bactopia citations --name mashtree
Mashtree
Katz LS, Griswold T, Morrison S, Caravas J, Zhang S, den Bakker HC, Deng X, Carleton HA
Mashtree: a rapid comparison of whole genome sequence files. Journal of Open Source
Software, 4(44), 1762 (2019)
```

Not much to this one, just an easy way to get citation information!

#### `bactopia download`

This command is very useful for setting up everything needed to run Bactopia. It will allow
you to pre-build Conda environments, pre-pull Docker contianers, and pre-download Singularity
images. If something exists it will not re-build it, so consider this a one time thing.

It is not recommended to share Conda environments accross users, but Docker and Singularity
can easily be shared across users.

```{bash}
bactopia download --help

 Usage: bactopia-download [OPTIONS] [UNKNOWN]...

 Builds Bactopia environments for use with Nextflow.

╭─ Required Options ───────────────────────────────────────────────────────────────────────╮
│ *  --bactopia    TEXT  Directory where Bactopia results are stored [required]            │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Build Related Options ──────────────────────────────────────────────────────────────────╮
│ --envtype                       [conda|docker|singularity|  The type of environment to   │
│                                 all]                        build.                       │
│                                                             [default: conda]             │
│ --wf                            TEXT                        Build a environment for a    │
│                                                             the given workflow           │
│                                                             [default: bactopia]          │
│ --condadir                      TEXT                        Directory to create Conda    │
│                                                             environments                 │
│                                                             (NXF_CONDA_CACHEDIR env      │
│                                                             variable takes precedence)   │
│                                                             [default:                    │
│                                                             /home/robert_petit/.bactopi… │
│ --use_conda                                                 Use Conda for building Conda │
│                                                             environments instead of      │
│                                                             Mamba                        │
│ --singularity_cache             TEXT                        Directory to download        │
│                                                             Singularity images           │
│                                                             (NXF_SINGULARITY_CACHEDIR    │
│                                                             env variable takes           │
│                                                             precedence)                  │
│                                                             [default:                    │
│                                                             /home/robert_petit/.bactopi… │
│ --singularity_pull_docker_c…                                Force conversion of Docker   │
│                                                             containers, instead          │
│                                                             downloading Singularity      │
│                                                             images directly              │
│ --force_rebuild                                             Force overwrite of existing  │
│                                                             pre-built environments.      │
│ --max_retry                     INTEGER                     Maximum times to attempt     │
│                                                             creating Conda environment.  │
│                                                             (Default: 3)                 │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Additional Options ─────────────────────────────────────────────────────────────────────╮
│ --verbose      Print debug related text.                                                 │
│ --silent       Only critical errors will be printed.                                     │
│ --version      Show the version and exit.                                                │
│ --help         Show this message and exit.                                               │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Options ────────────────────────────────────────────────────────────────────────────────╮
│ --build-all         Builds all environments for Bactopia workflows                       │
│ --build-nfcore      Builds all nf-core related environments                              │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
```

### `bactopia search`

Ever needed to supplement you analysis with some public data, or wanted to reproduce an
analysis using samples from a Bioproject? `bactopia search` is the command for that. It take
your query and search it against ENA's API. From your search, any matches will have thier
associated metadata and Experiment accessions save to a file. You can then provide the
file containing Experiment accessions to Bactopia using the `--accessions` parameter. Bactopia
will take care of downloading the FASTQ files for you.

#### Usage

```{bash}
bactopia search --help

 Usage: bactopia-search [OPTIONS]

 Query against ENA and SRA for public accessions to process with Bactopia

╭─ Required Options ───────────────────────────────────────────────────────────────────────╮
│ *  --query  -q  TEXT  Taxon ID or Study, BioSample, or Run accession (can also be comma  │
│                       separated or a file of accessions)                                 │
│                       [required]                                                         │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Query Options ──────────────────────────────────────────────────────────────────────────╮
│ --exact-taxon                     Exclude Taxon ID descendants                           │
│ --limit             -l   INTEGER  Maximum number of results (per query) to return        │
│                                   [default: 1000000]                                     │
│ --accession-limit   -al  INTEGER  Maximum number of accessions to query at once          │
│                                   [default: 5000]                                        │
│ --biosample-subset       INTEGER  If a BioSample has multiple Experiments, maximum       │
│                                   number to randomly select (0 = disabled)               │
│                                   [default: 0]                                           │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Filtering Options ──────────────────────────────────────────────────────────────────────╮
│ --min-base-count   -mbc  INTEGER  Filters samples based on minimum base pair count (0 =  │
│                                   disabled)                                              │
│                                   [default: 0]                                           │
│ --min-read-length  -mrl  INTEGER  Filters samples based on minimum mean read length (0 = │
│                                   disabled)                                              │
│                                   [default: 0]                                           │
│ --min-coverage     -mc   INTEGER  Filter samples based on minimum coverage (requires     │
│                                   --genome_size, 0 = disabled)                           │
│                                   [default: 0]                                           │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Additional Options ─────────────────────────────────────────────────────────────────────╮
│ --genome-size  -gsize  INTEGER  Genome size to be used for all samples, and for          │
│                                 calculating min coverage                                 │
│                                 [default: 0]                                             │
│ --outdir       -o      TEXT     Directory to write output [default: ./]                  │
│ --prefix       -p      TEXT     Prefix to use for output file names [default: bactopia]  │
│ --force                         Overwrite existing reports                               │
│ --verbose                       Increase the verbosity of output                         │
│ --silent                        Only critical errors will be printed                     │
│ --version      -V               Show the version and exit.                               │
│ --help                          Show this message and exit.                              │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
```

#### Example Searches

Let's do a quick search for *Staphylococcus aureus* and limit the result to only 10 matches.
To do this will use `--query` and `--limit`.

```{bash}
bactopia search --query "Staphylococcus aureus" --limit 10
2023-03-13 18:09:39 INFO     2023-03-13 18:09:39:root:INFO - Submitting query  search.py:523
                             (type - taxon_name)
2023-03-13 18:09:43 INFO     2023-03-13 18:09:43:root:INFO - Writing results   search.py:603
                             to ./bactopia-metadata.txt
                    INFO     2023-03-13 18:09:43:root:INFO - Writing           search.py:610
                             accessions to ./bactopia-accessions.txt
                    INFO     2023-03-13 18:09:43:root:INFO - Writing filtered  search.py:615
                             accessions to ./bactopia-filtered.txt
                    INFO     2023-03-13 18:09:43:root:INFO - Writing summary   search.py:621
                             to ./bactopia-search.txt
```

Please feel free to submit your own queries, and play around with ways to filter the data
(e.g. `--min-base-count`, `--min-read-length`, `--min-coverage`).

### `bactopia prepare`

Bactopia allows you to process a single sample at a time, or thousands at a time. If you have
many FASTQ files you would like to process, and you don't want to manually run them one at a
time, you can instead provide a *file of filenames (FOFN)*. `bactopia prepare` can help you
create this FOFN. It is important to note, due to Nextflow support of alternate file systems,
the files you want to include in your FOFN, do not have to be local. You can include files
that are on each of the three major cloud platforms.

#### Usage

```{bash}
bactopia prepare --help

 Usage: bactopia-prepare [OPTIONS]

 Create a 'file of filenames' (FOFN) of samples to be processed by Bactopia

╭─ Required Options ───────────────────────────────────────────────────────────────────────╮
│ *  --path  -p  TEXT  Directory where FASTQ files are stored [required]                   │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Matching Options ───────────────────────────────────────────────────────────────────────╮
│ --assembly-ext     -a  TEXT  Extension of the FASTA assemblies [default: .fna.gz]        │
│ --fastq-ext        -f  TEXT  Extension of the FASTQs [default: .fastq.gz]                │
│ --fastq-separator      TEXT  Split FASTQ name on the last occurrence of the separator    │
│                              [default: _]                                                │
│ --pe1-pattern          TEXT  Designates difference first set of paired-end reads         │
│                              [default: [Aa]|[Rr]1|1]                                     │
│ --pe2-pattern          TEXT  Designates difference second set of paired-end reads        │
│                              [default: [Bb]|[Rr]2|2]                                     │
│ --merge                      Flag samples with multiple read sets to be merged by        │
│                              Bactopia                                                    │
│ --ont                        Single-end reads should be treated as Oxford Nanopore reads │
│ --recursive        -r        Directories will be traversed recursively                   │
│ --prefix               TEXT  Prefix to add to the path                                   │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Sample Information Options ─────────────────────────────────────────────────────────────╮
│ --metadata             TEXT     Metadata per sample with genome size and species         │
│                                 information                                              │
│ --genome-size  -gsize  INTEGER  Genome size to use for all samples                       │
│ --species      -s      TEXT     Species to use for all samples (If available, can be     │
│                                 used to determine genome size)                           │
│ --taxid                TEXT     Use the genome size of the Taxon ID for all samples      │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Additional Options ─────────────────────────────────────────────────────────────────────╮
│ --examples        Print example usage                                                    │
│ --verbose         Increase the verbosity of output                                       │
│ --silent          Only critical errors will be printed                                   │
│ --version   -V    Show the version and exit.                                             │
│ --help            Show this message and exit.                                            │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
```


### `bactopia summary`

After a successful run the `bactopia summary` command will aggregate results from each of the
samples into a single TSV file. You can then load this summary TSV into a Jupyter or RMarkdown
noteboot, and begin some exploratory analysis.

The `bactopia summary` command is not meant to be a *report* per-say, true reports will follow
in the future.

#### Usage

```{bash}
bactopia summary --help

 Usage: bactopia-summary [OPTIONS]

 Generate a summary table from the Bactopia results.

╭─ Required Options ───────────────────────────────────────────────────────────────────────╮
│ *  --bactopia  -b  TEXT  Directory where Bactopia results are stored [required]          │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Gold Cutoffs ───────────────────────────────────────────────────────────────────────────╮
│ --gold-coverage     -gcov      INTEGER  Minimum amount of coverage required for Gold     │
│                                         status                                           │
│                                         [default: 100]                                   │
│ --gold-quality      -gqual     INTEGER  Minimum per-read mean quality score required for │
│                                         Gold status                                      │
│                                         [default: 30]                                    │
│ --gold-read-length  -glen      INTEGER  Minimum mean read length required for Gold       │
│                                         status                                           │
│                                         [default: 95]                                    │
│ --gold-contigs      -gcontigs  INTEGER  Maximum contig count required for Gold status    │
│                                         [default: 100]                                   │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Silver Cutoffs ─────────────────────────────────────────────────────────────────────────╮
│ --silver-coverage     -scov      INTEGER  Minimum amount of coverage required for Silver │
│                                           status                                         │
│                                           [default: 50]                                  │
│ --silver-quality      -squal     INTEGER  Minimum per-read mean quality score required   │
│                                           for Silver status                              │
│                                           [default: 20]                                  │
│ --silver-read-length  -slen      INTEGER  Minimum mean read length required for Silver   │
│                                           status                                         │
│                                           [default: 75]                                  │
│ --silver-contigs      -scontigs  INTEGER  Maximum contig count required for Silver       │
│                                           status                                         │
│                                           [default: 200]                                 │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Fail Cutoffs ───────────────────────────────────────────────────────────────────────────╮
│ --min-coverage        -mincov   INTEGER  Minimum amount of coverage required to pass     │
│                                          [default: 20]                                   │
│ --min-quality         -minqual  INTEGER  Minimum per-read mean quality score required to │
│                                          pass                                            │
│                                          [default: 12]                                   │
│ --min-read-length     -minlen   INTEGER  Minimum mean read length required to pass       │
│                                          [default: 49]                                   │
│ --max-contigs                   INTEGER  Maximum contig count required to pass           │
│                                          [default: 500]                                  │
│ --min-assembled-size            INTEGER  Minimum assembled genome size                   │
│ --max-assembled-size            INTEGER  Maximum assembled genome size                   │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
╭─ Additional Options ─────────────────────────────────────────────────────────────────────╮
│ --outdir   -o  PATH  Directory to write output [default: ./]                             │
│ --prefix   -p  TEXT  Prefix to use for output files [default: bactopia]                  │
│ --force              Overwrite existing reports                                          │
│ --verbose            Increase the verbosity of output                                    │
│ --silent             Only critical errors will be printed                                │
│ --version  -V        Show the version and exit.                                          │
│ --help               Show this message and exit.                                         │
╰──────────────────────────────────────────────────────────────────────────────────────────╯
```

We'll circle back to this one.

### The `test` profile

So we've played around with the subcommands, let's see if Bactopia actually works. For this
we can use the `test` profile. Modeled after nf-core, this profile will process a super small
(~300kb) bacterial genome. The benefit is the test is super quick while also informing you
if everything is setup properly.

Let's give it a try:

```{bash}
mkdir bactopia-workshop
cd bactopia-workshop
bactopia -profile test,singlarity
```

Notice here, Nextflow allows you to provide multiple profiles when separated by a comma. So,
what we're doing here is telling Nextflow to run the test profile, and use Singularity. By
default Bactopia would have used Conda.

Upon completion, please feel free to start browsing some of the results and the logs.


For this workshop we'll be telling Nextflow to use Singularity. Sometimes it can get a little
confusing, yes we used Conda to install Bactopia, but for the actual analysis we'll be using
Singularity. This is handled by the `-profile` parameter, which is a Nextflow parameter that
selects an executor (e.g. Docker, Conda, AWS, etc...)

For those that want to jump a ahead, there is a [Bactopia Tutorial]() that you can follow.
This tutorial will have you processing some *Staphylococcus aureus* genomes, and playing
with some of the sub-commands. A small warning though, there may be some changes needed in
order account for v3 changes, so you might be able to copy and paste.
