<img src="img/primrose-logo.png" alt="primrose logo" width="200px" align="right"/>
<h1 align="center">Primrose</h1>
<p align="center">Predict 5mC in PacBio HiFi reads</p>

***

*Primrose* predicts 5-Methylcytosine (5mC) of each CpG in PacBio HiFi reads, using
a Convolutional Neural Network. Methylation is assumed to be symmetric between
strands. The output is reported in the forward direction with respect to the HiFi
read sequence.

## Availability
Latest version can be installed via bioconda package `primrose`.

Please refer to our [official pbbioconda page](https://github.com/PacificBiosciences/pbbioconda)
for information on Installation, Support, License, Copyright, and Disclaimer.

## Latest Version
Version **1.3.0**: [Full changelog here](#changelog)

## Input Data
Input for *primrose* are PacBio HiFi reads with kinetics. You can generate HiFi
with kinetics on the command-line, more info on [ccs.how](https://ccs.how/):

    ccs movie.subreads.bam movie.hifi_reads.bam --hifi-kinetics

Alternatively, you can use *SMRT Link* on your HPC or define it directly in *Run
Design* for SQIIe instruments.

## Execution
Running *primrose* is as simple as:

    primrose movie.hifi_reads.bam movie.5mc.hifi_reads.bam

## Output Data
The output is adhering to the [SAM tag specification from 9. Dec 2021](https://samtools.github.io/hts-specs/SAMtags.pdf),
using `Mm` and `Ml` tags. It's also described in the [PacBio BAM file formats](https://pacbiofileformats.readthedocs.io/en/latest/BAM.html#use-of-read-tags-for-per-read-base-base-modifications) as

| Tag  | Type  |           Description            |
| ---- | ----- | -------------------------------- |
| `Mm` | `Z`   | Base modifications / methylation |
| `Ml` | `B,C` | Base modification probabilities  |

Notes for `Ml`: The continuous probability range of 0.0 to 1.0 is remapped to
the discrete integers 0 to 255 inclusively. The probability range corresponding
to an integer N is `N/256` to `(N + 1)/256`.

## Run Time
*primrose* scales nearly linear in the number of threads, achieving 2 GBases HiFi per minute on
16 cores. Memory footprint is very low with ~20 MB per thread.

    $ primrose movie.hifi_reads.bam out.bam -j 16 --log-level INFO
    Reads      : 100000
    Yield      : 1.8 GBases
    Throughput : 2.0 GBases/min
    Run Time   : 54s 904ms
    CPU Time   : 16m 52s
    Peak RSS   : 0.313 GB

## Single-molecule performance
### Data
True negatives - HG002 Whole Genome Amplification (WGA).\
True positives - HG002 WGA + CpG Methyltransferase (M.Sssl).

### Metrics
 * **Precision**: 86%
 * **Recall**: 85%
 * **Accuracy**: 85%

## Changelog

 * **1.3.0**
   * Latest developer version

 * 1.2.0
   * Included in upcoming SMRT Link version
   * Use official basemod tags `MM` and `ML` per SAM spec Feb 2022

 * 1.1.0
   * Add CLI call into new `@PG` header lines
   * Allow multiple `BAM` files via `XML` input

 * 1.0.0
   * Initial release
