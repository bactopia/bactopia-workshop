# Using Bactopia Tools

Nice, you're ready to start playing around with Bactopia Tools!. Please if you haven't already,
you will need to have completed [Using Bactopia](/workshop/03-bactopia.md).

Bactopia Tools, are additional workflows to further analyze your samples. Using the
standard directory structure from Bactopia, Bactopia Tools will automatically import
the input files required by a specific tool.

Currently there are over 50, of these Bactopia Tools available!

This section is going to be a little different, you are going to kind of be on your
own to explore, so have fun!

## Goals of this Section

- [ ] Learn about available Bactopia Tools
- [ ] Run multiple Bactopia Tools

## Learn about available Bactopia Tools

First, if you would like, you can head over to [Bactopia Tools](https://bactopia.github.io/v2.2.0/bactopia-tools/)
on the documentation site. Here you can learn alot about the available Bactopia Tools, including:

1. Example Usage
2. Output Overview
3. Parameters
4. Citations

But you can also, get a list on the command-line:

```{bash}
bactopia --list_wfs
---------------------------------------------
   _                _              _
  | |__   __ _  ___| |_ ___  _ __ (_) __ _
  | '_ \ / _` |/ __| __/ _ \| '_ \| |/ _` |
  | |_) | (_| | (__| || (_) | |_) | | (_| |
  |_.__/ \__,_|\___|\__\___/| .__/|_|\__,_|
                            |_|
  bactopia v3.0.0
  Bactopia is a flexible pipeline for complete analysis of bacterial genomes
---------------------------------------------
Below are a list of workflows you can call using the --wf parameter.

Bactopia
  bactopia (default)          Bactopia is a flexible pipeline for complete analysis of bacterial genomes
  cleanyerreads               Use Bactopia's read QC steps to Clean-Yer-Reads
  staphopia                   Staphopia is a flexible pipeline for complete analysis of Staphylococcus aureus genomes
  teton                       Host removal and taxon classification with estimated abundances

Bactopia Tools
Bactopia Tools can include multiple tools (Subworkflows) or a single tool (Modules).

Subworkflows (9)
  ariba                       Gene identification through local assemblies
  bakta                       Rapid annotation of bacterial genomes & plasmids
  eggnog                      Functional annotation of proteins using orthologous groups and phylogenies
  gtdb                        Identify marker genes and assign taxonomic classifications
  merlin                      MinmER assisted species-specific bactopia tool seLectIoN
  pangenome                   Pangenome analysis with optional core-genome phylogeny
  scrubber                    Scrub human reads from FASTQ files
  snippy                      Rapid variant calling from Illumina sequence reads with optional core-SNP phylogeny
  staphtyper                  Includes AgrVATE, SpaTyper and Staphopia SCCmec for Staphylococcus aureus

Modules (43)
  abricate                    Mass screening of contigs for antimicrobial and virulence genes
  agrvate                     Rapid identification of Staphylococcus aureus agr locus type and agr operon variants
  amrfinderplus               Identify antimicrobial resistance in genes or proteins
  bracken                     Taxonomic classification ans species abundance estimation of sequence reads
  busco                       Assembly completeness based on evolutionarily informed expectations
  checkm                      Assess the assembly quality of your microbial samples
  ectyper                     In-silico prediction of Escherichia coli serotype
  emmtyper                    emm-typing of Streptococcus pyogenes assemblies
  fastani                     Fast alignment-free computation of whole-genome Average Nucleotide Identity (ANI)
  gamma                       Identification, classification, and annotation of translated gene matches
  genotyphi                   Salmonella Typhi genotyping with Mykrobe outputs
  hicap                       Identify cap locus serotype and structure in your Haemophilus influenzae assemblies
  hpsuissero                  Rapid Haemophilus parasuis Serotyping of assemblies
  ismapper                    Identify insertion sites positions in bacterial genomes
  kleborate                   Screen for MLST, sub-species, and other Klebsiella related genes of interest
  kraken2                     Taxonomic classification of sequence reads
  legsta                      Typing of Legionella pneumophila assemblies
  lissero                     Serogroup typing prediction for Listeria monocytogenes
  mashdist                    Calculate Mash distances between sequences
  mashtree                    Create a trees using Mash distance
  mcroni                      Sequence variation in mobilized colistin resistance (mcr-1) genes
  meningotype                 Serotyping of Neisseria meningitidis
  midas                       Estimate species abundances from FASTQ files
  mlst                        Scan contig files against PubMLST typing schemes
  mobsuite                    Reconstruct and annotate plasmids in bacterial assemblies
  mykrobe                     Antimicrobial resistance detection for specific species
  ngmaster                    Multi-antigen sequence typing for Neisseria gonorrhoeae
  pasty                       Serogrouping of Pseudomonas aeruginosa isolates
  pbptyper                    Penicillin Binding Protein (PBP) typer for Streptococcus pneumoniae
  plasmidfinder               Plasmid identification from assemblies
  prokka                      Whole genome annotation of small genomes (bacterial, archeal, viral)
  rgi                         Predict antibiotic resistance from assemblies
  seqsero2                    Salmonella serotype prediction from reads or assemblies
  seroba                      Serotyping of Streptococcus pneumoniae from sequence reads
  shigatyper                  Shigella serotype from Illumina or Oxford Nanopore reads
  shigeifinder                Shigella and EIEC serotyping from assemblies
  sistr                       Serovar prediction of Salmonella assemblies
  spatyper                    Computational method for finding spa types in Staphylococcus aureus
  ssuissero                   Rapid Streptococcus suis Serotyping of assemblies
  staphopiasccmec             Primer based SCCmec typing of Staphylococcus aureus genomes
  stecfinder                  Serotyping Shigella toxin producing Escherichia coli genomes
  tbprofiler                  Detect resistance and lineages of Mycobacterium tuberculosis genomes
  updater                     Update database builds for new Bactopia version
```

Each one of these Bactopia Tools can be executed by simply changing
the workflow with the `--wf` parameter.

## Run Multiple Bactopia Tools

This is the part where you get to try running Bactopia Tools you might be interested in.

We'll do a few together, let's try `pasty` first. To do this will need to provide a few parameters
`--bactopia` and `--wf`.

The `--bactopia` parameter should point to the path where your Bactopia outputs are. This will be
the folder before `bactopia-samples`. In this workshop we didn't change `--outdir` when running
Bactopia, so the default output folder will be `bactopia`.

Let's give it a try.

```{bash}
bactopia \
    --bactopia bactopia/ \
    --wf pasty \
    -profile singularity \
    --help
```

Notice I added a `--help` in there. I want you to be aware that every workflow (`--wf`) has
its own parameters. If you are ever curious, `--help` and `--help_all` are available for
every workflow, with `--help_all` displaying the hidden parameters.

This time for real.

```{bash}
bactopia \
    --bactopia bactopia/ \
    --wf pasty \
    -profile singularity
```

When it completes, you will now have a `pasty` folder for each sample in `bactopia-samples`.
As well as one in `bactopia-comparative` which is a concatenation of all the results for
each sample.

Again, from here I think it's time go out and explore. 

## Wrap Up

I hope you were able to run multiple Bactopia Tools, and now understand how
useful they can be in your analysis. One thing to keep in mind, each of these
Bactopia Tools is plug-and-play, so if you can easily move them around. For example,
the `staphopia` workflow, is just the `bactopia` workflow with the `staphtyper`
Bactopia Tool added to it. You can imagine how you might want to always create
a quick tree at the end of a run, for this you could simply add the `mashtree`
Bactopia Tool to a workflow.

Many many possibilities when it comes to customizing workflows now. Please feel free
to keep exploring and playing around.

If you have any issues or feedback, please don't hesitate to reach out!
