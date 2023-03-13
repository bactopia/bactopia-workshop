# Exploring Bactopia Subcommands

Before we start exploring the Bactopia Subcommands, please make sure you have completed
[Getting Setup](/workshop/01-getting-setup.md).

OK, Bactopia is installed, you are all set to process all the genomes. But first, let's
take a look at Bactopia subcommands are *helper* commands that can improve your usage
Bactopia. Each of these subcommands has recently been rewritten and made available from
a Python package called [bactopia-py](https://github.com/bactopia/bactopia-py). 

## Goals inn this Section

The goal of this section is to expose you to each of the Bactopia subcommands. For some of
these commands we'll take a quick look at, for others we'll take a deeper look into. 

- [ ] `bactopia citations`
- [ ] `bactopia download`
- [ ] `bactopia prepare`
- [ ] `bactopia search`
- [ ] `bactopia summary`

## `bactopia citations`

`bactopia citations` is a nice and simple way to get the citation information for all tools
used by Bactopia. By default it will print out all citations, but you can filter using the
`--name` parameter, for example:

```{bash}
bactopia citations --name abricate
Abricate
Seemann T Abricate: mass screening of contigs for antimicrobial and virulence genes (GitHub)

bactopia citations --name bakta
Bakta
Schwengers O, Jelonek L, Dieckmann MA, Beyvers S, Blom J, Goesmann A Bakta - rapid and
standardized annotation of bacterial genomes via alignment-free sequence identification.
Microbial Genomics 7(11) (2021)

bactopia citations --name mashtree
Mashtree
Katz LS, Griswold T, Morrison S, Caravas J, Zhang S, den Bakker HC, Deng X, Carleton HA
Mashtree: a rapid comparison of whole genome sequence files. Journal of Open Source
Software, 4(44), 1762 (2019)

bactopia citations --name seqsero2
SeqSero2
Zhang S, Den-Bakker HC, Li S, Dinsmore BA, Lane C, Lauer AC, Fields PI, Deng X. SeqSero2:
rapid and improved Salmonella serotype determination using whole genome sequencing data.
Appl Environ Microbiology 85(23):e01746-19 (2019)
```

You other than that, there really isn't much else to know about this command. It's just an
easy way to get citation information!

## `bactopia download`

This command is very useful for setting up everything needed to run Bactopia. It will allow
you to pre-build Conda environments, pre-pull Docker contianers, and pre-download Singularity
images. If something exists it will not re-build it, so consider this a one time thing.

It is not recommended to share Conda environments accross users, but Docker and Singularity
can easily be shared across users.

In most cases, you will not need to manually run this command as its run before each Bactopia
run. However, in situations where you might share a singularity folder, it is easier to
manually run `bactopia download` and call it a day!

Also, it is worth mentioning Bactopia will respect the Nextflow `NXF_SINGULARITY_CACHEDIR`
environment variable. So, if you plan on sharing Singularity images, definitely consider
adding `NXF_SINGULARITY_CACHEDIR` to your environment.

<details>
  <summary>View Usage</summary>

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
 
</details>
 
Won't go into much more details about `bactopia download` as most of the time it's just in the
background.

## `bactopia search`

Now `bactopia search` is a very useful tool. Think to yourself, have you ever needed to supplement
you analysis with some public data, or wanted to reproduce an analysis using samples from a Bioproject?
Then you have to go through the effort of downloading all the associated FASTQ files.

Well, `bactopia search` is the command for that. It take your query and search it against ENA's API.
From your search, any matches will have their associated metadata and Experiment accessions saved to
a file. You can then provide the file containing Experiment accessions to Bactopia using the
`--accessions` parameter. With this list of accessions, Bactopia will take care of downloading each of the
FASTQ files for you. Then, proceed to process each sample through Bactopia.

This is a command we'll take a look at. So for starters, let's take a look at the usage.

```{bash}
bactopia search --help
```

<details>
  <summary>View Usage</summary>

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

</details>

Ok, now let's do a quick search for *Staphylococcus aureus* and limit the result to only 10 matches.
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

With this query you will get a few files:

| Filename                  | Description                                                                     |
|---------------------------|---------------------------------------------------------------------------------|
| `<PREFIX>-accessions.txt` | A list of matching Experiment accessions, that be used as an input for Bactopia |
| `<PREFIX>-filtered.txt`   | Any matching samples, and reason, that might have been filtered out             |
| `<PREFIX>-metadata.txt`   | Available metadata associated with each matching sample                         |
| `<PREFIX>-search.txt`     | Details about the search query                                                  |

The `accessions.txt` file is going to be the file that you can provide to 

### Additional Exercises

Please feel free to submit your own queries, and play around with ways to filter the data
(e.g. `--min-base-count`, `--min-read-length`, `--min-coverage`).

For example, selecting only *Staphylococcus aureus* samples with at least 100x coverage:

```{bash}
bactopia search --query 1280 --min-coverage 100 --genome-size 2800000 --prefix saureus100x
```

*Notice here I used a taxid!*

Finally, another thing to consider about `bactopia search`, even if you don't want to use Bactopia, `bactopia search`
is a simple tool to query ENA's API. It has been used to describe the state of bacterial seqeuncing, and a
method to quickly convert accessions (e.g. BioSample) to Experiment accessions.


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
