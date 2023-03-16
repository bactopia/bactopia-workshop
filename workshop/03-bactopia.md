# Using Bactopia

OK, if you are here, you're ready to start playing around with Bactopia. But, first
if you didn't already, consider taking a look at the [Bactopia Subcommands](/workshop/02-bactopia-subcommands.md).

Bactopia is the main pipeline for bacterial genome analysis. It will take your samples from
raw reads, to assembled and annotated genomes, among many other analyses.

For this section we'll be telling Nextflow to use Singularity. Sometimes it can get a little
confusing, yes we used Conda to install Bactopia, but for the actual analysis we'll be using
Singularity. If Singulartity is not available for you, you can also use Conda or Docker. This
is handled by the `-profile` parameter, which is a Nextflow parameter that selects an executor
(e.g. `-profile docker`, `-profile standard`, `-profile slurm`, etc...)

For those that want to jump a ahead, there is a [Bactopia Tutorial](https://bactopia.github.io/v2.2.0/tutorial/)
that you can follow. This tutorial will have you processing *Staphylococcus aureus* genomes,
and playing with some of the sub-commands. A small warning though, there may be some changes
needed in order account for v3 changes, so you might not be able to copy and paste.

## Goals of this Section

There are many ways to process samples with Bactopia. Time permitting, we'll attempt to go
through each way.

- [ ] Run the `test` profile
- [ ] Process a paired-end Illumina sample
- [ ] Process a single-end Illumina sample
- [ ] Process a Nanopore sample
- [ ] Process a sample with both Illumina and Nanopore
- [ ] Process samples with a FOFN (`bactopia prepare`)
- [ ] Process samples with accessions (`bactopia search`)
- [ ] Process an assembly

## Run the `test` profile

For this we can use the `test` profile. Modeled after nf-core, this profile will process
a super small (~300kb) bacterial genome. A major benefit of the test profile, is it allows
for a uper quick way to determine if everything is setup properly.

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

Bactopia includes multiple other built in profiles. If interested check out
[Bactopia profiles](https://github.com/bactopia/bactopia/tree/master/conf/profiles). Each
of these can be used by simply adding `-profile <PROFILE_NAME>`.

## Processing Samples

Bactopia provides you with many ways to process a sample. You can process:

- Illumina paired-end and single-end reads
- Oxford Nanopore reads
- Both Illumina and ONT reads together
  - Hybrid assembly short-read polishing or with Unicycler
- Assemblies
- DDBJ/ENA/SRA Experiment accessions
- NCBI Assembly Accessions

Here are the parameters to handle all this:

```{bash}
  ### For Procesessing Multiple Samples
  --samples                           [string]  A FOFN (via bactopia prepare) with sample names and paths to FASTQ/FASTAs to process

  ### For Processing A Single Sample
  --R1                                [string]  First set of compressed (gzip) paired-end FASTQ reads (requires --R2 and --sample)
  --R2                                [string]  Second set of compressed (gzip) paired-end FASTQ reads (requires --R1 and --sample)
  --SE                                [string]  Compressed (gzip) single-end FASTQ reads  (requires --sample)
  --ont                               [boolean] Treat `--SE` or `--accession` as long reads for analysis. (requires --sample if using --SE)
  --hybrid                            [boolean] Treat `--SE` as long reads for hybrid assembly.  (requires --R1, --R2, --SE and --sample)
  --short_polish                      [boolean] Treat `--SE` as long reads for long-read assembly and short read polishing.  (requires --R1, --R2, --SE and
                                                --sample)
  --sample                            [string]  Sample name to use for the input sequences

  ### For Downloading from SRA/ENA or NCBI Assembly
  **Note: Downloaded assemblies will have error free Illumina reads simulated for processing.**
  --accessions                        [string]  A file containing ENA/SRA Experiment accessions or NCBI Assembly accessions to processed
  --accession                         [string]  Sample name to use for the input sequences

  ### For Processing an Assembly
  **Note: Assemblies will have error free Illumina reads simulated for processing.**
  --assembly                          [string]  A assembled genome in compressed FASTA format. (requires --sample)
  --check_samples                     [boolean] Validate the input FOFN provided by --samples
```

Time permitting we'll go through each one of these methods. To do this, we'll
be using data from [bactopia-tests](https://github.com/bactopia/bactopia-tests).
These are super small, and should hopefully complete quickly.

### Paired-end Illumina sample

For paired-end Illumina reads, we'll need to use `--R1`, `--R2`, `--sample`. In addition
you should include an expected genome size of your sample, via `--genome_size`.

Let's give it a try:

```{bash}
bactopia \
  --R1 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R1.fastq.gz \
  --R2 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R2.fastq.gz \
  --sample test-pe \
  --run_name test-pe \
  --genome_size 358000 \
  -profile singularity
```

That should be all that's needed to run a single Illumina paired-end sample. Please feel
free to browse the results.

### Process a single-end Illumina sample

For single-end Illumina reads, we will instead need to use `--SE` and `--sample`. Again,
please include an expected genome size using `--genome_size`.

```{bash}
bactopia \
  --SE https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702SE.fastq.gz \
  --sample test-se \
  --run_name test-se \
  --genome_size 358000 \
  -profile singularity
```

Alright! You should now have some single-end results to take a look at.

### Process a Nanopore sample

Nanopore reads are single-end, so we'll still be using the `--SE` parameter. However
we'll tell Bactopia to treat the provided single-end reads as ONT reads, by adding
the `--ont` parameter. We'll still need `--sample`, and it's highly recommended you
continue to provide a genome size (`--genome_size`).

```{bash}
bactopia \
  --SE https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/nanopore/ERR3772599.fastq.gz \
  --ont \
  --sample test-ont \
  --run_name test-ont \
  --genome_size 358000 \
  -profile singularity
```

Look at that, first Illumina paired-end, then single-end reads, and now ONT reads processed.
Again, feel free to take a look at the results. They will be a little different this
time around because ONT uses different tools for processed compared to Illumina.

### Process a sample with both Illumina and Nanopore

OK, let's bring them together. You have Illumina reads and Nanopore reads for the same
sample. Bactopia gives you the option for a ONT assembly with short-read polishing (personal
recommendation with sufficient ONT coverage), or an hybrid assembly with Unicycler.

For this, we'll need to use `--R1`, `--R2`, `--SE` and `--sample`, in addition to `--short_polish`
or `--hybrid`.

#### `--short_polish`

When providing `--short_polish` the ONT reads will be considered the primary set of reads.
So, the ONT reads will processed, then at the assembly step the Illumina reads will be used
for polishing the ONT assembly.

```{bash}
bactopia \
  --R1 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R1.fastq.gz \
  --R2 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R2.fastq.gz \
  --SE https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/nanopore/ERR3772599.fastq.gz \
  --short_polish \
  --sample test-short-polish \
  --run_name test-short-polish \
  --genome_size 358000 \
  -profile singularity
```

#### `--hybrid`

As an alternative, you can use `--hybrid` to use Unicycler to create a hybrid assembly. Unicycler
will assemble with the Illumina reads, then try to bridge contigs using the ONT reads.

```{bash}
bactopia \
  --R1 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R1.fastq.gz \
  --R2 https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/illumina/SRR2838702_R2.fastq.gz \
  --SE https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/nanopore/ERR3772599.fastq.gz \
  --hybrid \
  --sample test-hybrid \
  --run_name test-hybrid \
  --genome_size 358000 \
  -profile singularity
```

ONT assembly is quite sufficient and fast these days, that it is often better to go the
`--short_polish` route, and let the Illumina reads correct errors in the ONT assembly.

However, you know your data better, and based on your data you should select the method
you think is most appropriate.

### Process an assembly

Honestly by this point, you might be done with processing all these samples. But hang
in there we're almost done!

Sometimes, you might have a sample you want to include in your study, but only an
assembly is available. Bactopia will process these samples for you.

When providing an assembly, Illumina PE error-free reads are simulated using ART to
allow for steps that only work with FASTQs, and the assemblies will not be reassembled
at the assembly step.

```{bash}
bactopia \
  --assembly https://github.com/bactopia/bactopia-tests/raw/main/data/species/portiera/genome/GCF_000292685.fna.gz \
  --sample test-assembly \
  --run_name test-assembly \
  --genome_size 358000 \
  -profile singularity
```

There you go, now you can include assemblies in your studies.

### Process an accession

The last way to process a single sample, is to use an accession from public databases.

```{bash}
bactopia \
  --accession SRX1390609 \
  --sample test-accession \
  --run_name test-accession \
  --genome_size 358000 \
  -profile singularity
```

## Processing Multiple Samples

### Process samples with a FOFN (`bactopia prepare`)

For the purposes of this workshop, we're going to reuse the FASTQs we allready have
to create a FOFN with `bactopia prepare`. But by all means if you have your own
data, give it a go!

```{bash}
bactopia prepare \
    --path bactopia/bactopia-samples/ \
    --recursive \
    --assembly-ext . \
    --genome-size 358000 --ont > samples.txt
    
# Run the samples using --samples
bactopia \
  --samples samples.txt \
  --run_name test-fofn \
  -profile singularity
```

### Process samples with accessions (`bactopia search`)

Sometimes you might want to include public data in your analysis,
for this you can generate a list of accessions using `bactopia search`.
Once your search completes, you will have a file that ends in
`*-accessions.txt` this file can be passed to Bactopia using the
`--accessions` parameter. Let's give it a go!

```{bash}
# Grab a few Mycoplasmoides genitalium  (taxid 2097) genomes
# using bactopia search
bactopia search --query 2097 --prefix multiple --limit 5

# Run the samples using --accessions
bactopia \
  --accessions multiple-accessions.txt \
  --run_name test-accessions \
  --genome_size 358000 \
  -profile singularity
```

## Wrap Up!

Hopefully you were able to successfully run each command! By now, I
hope you can appreciate there are many ways to process samples using
Bactopia.

Now, it's time to head on over to [Bactopia Tools](/workshop/04-bactopia-tools.md)!
