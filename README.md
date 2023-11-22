[![Tests](https://github.com/bede/hostile/actions/workflows/test.yml/badge.svg)](https://github.com/bede/hostile/actions/workflows/test.yml) [![PyPI version](https://img.shields.io/pypi/v/hostile)](https://pypi.org/project/hostile/) [![Bioconda version](https://anaconda.org/bioconda/hostile/badges/version.svg)](https://anaconda.org/bioconda/hostile/) [![DOI:10.1101/2023.07.04.547735](http://img.shields.io/badge/BioRxiv-10.1101/2023.07.04.547735-bd2736.svg)](https://www.biorxiv.org/content/10.1101/2023.07.04.547735) ![Downloads](https://static.pepy.tech/badge/hostile)

<p align="center">
    <img width="250" src="logo.png">
</p>

# Hostile

Hostile removes host sequences from short and long reads, consuming paired or unpaired `fastq[.gz]` input. Batteries are included – a human reference genome is downloaded when run for the first time. Hostile is precise by default, removing an [order of magnitude fewer microbial reads](https://log.bede.im/2023/08/29/precise-host-read-removal.html#evaluating-accuracy) than existing approaches while removing >99.5% of real human reads from 1000 Genomes Project samples. For ultimate precision, a prebuilt masked reference can be downloaded, or a new one created for chosen target organisms. Read headers can be replaced with integers (using `--rename`) for privacy and smaller FASTQs. Heavy lifting is done with fast existing tools (Minimap2/Bowtie2 and Samtools). Bowtie2 is the default aligner for short (paired) reads while Minimap2 is default aligner for long reads. Further information and benchmarks can be found in the [BioRxiv preprint](https://www.biorxiv.org/content/10.1101/2023.07.04.547735) and this [blog post](https://log.bede.im/2023/08/29/precise-host-read-removal.html). Feel free open an issue, [tweet](https://twitter.com/beconsta), [toot](https://mstdn.science/@bede) or email me to report problems or suggest improvements.



## Reference genomes & indexes

The default `human-t2t-hla` index is a good choice for most use cases, and is downloaded automatically when running Hostile for the first time. Slightly higher microbial retention in may be achieved by specifying a custom `--index` masked against target organisms. The `human-t2t-hla-argos985` index is masked against [985 reference grade bacterial genomes](https://www.ncbi.nlm.nih.gov/bioproject/231221), making it a good choice for decontaminating bacterial genomes. Another masked genome `human-t2t-hla-argos985-mycob140` was created specifically for maximising the retention of mycobacterial genomes. Note that Bowtie2 indexes need to be untarred before use, while Minimap2 indexes are built at runtime from the specified genome in `fasta[.gz]` format. The index `human-t2t-hla` and `human-t2t-hla-argos985-mycob140` were compared in the [paper](https://www.biorxiv.org/content/10.1101/2023.07.04.547735).

|               Name                |                         Composition                          |                       Minimap2 genome                        |                        Bowtie2 index                         | Date    |
| :-------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | ------- |
|   `human-t2t-hla` **(default)**   | [T2T-CHM13v2.0](https://www.ncbi.nlm.nih.gov/assembly/11828891) + [IPD-IMGT/HLA](https://www.ebi.ac.uk/ipd/imgt/hla/) v3.51 | [human-t2t-hla.fa.gz](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla.fa.gz) | [human-t2t-hla.tar](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla.tar) | 2023-07 |
|     `human-t2t-hla-argos985`      | [T2T-CHM13v2.0](https://www.ncbi.nlm.nih.gov/assembly/11828891) & [IPD-IMGT/HLA](https://www.ebi.ac.uk/ipd/imgt/hla/) v3.51; masked with [985](https://github.com/bede/hostile/blob/main/paper/supplementary-table-2.tsv) [FDA-ARGOS](https://www.ncbi.nlm.nih.gov/bioproject/231221) 150mers | [human-t2t-hla-argos985.fa.gz](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla-argos985.fa.gz) | [human-t2t-hla-argos985.tar](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla-argos985.tar) | 2023-07 |
| `human-t2t-hla-argos985-mycob140` | [T2T-CHM13v2.0](https://www.ncbi.nlm.nih.gov/assembly/11828891) & [IPD-IMGT/HLA](https://www.ebi.ac.uk/ipd/imgt/hla/) v3.51; masked with [985](https://github.com/bede/hostile/blob/main/paper/supplementary-table-2.tsv) [FDA-ARGOS](https://www.ncbi.nlm.nih.gov/bioproject/231221) & [140](https://github.com/bede/hostile/blob/main/paper/supplementary-table-2.tsv) mycobacterial 150mers | [human-t2t-hla-argos985-mycob140.fa.gz](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla-argos985-mycob140.fa.gz) | [human-t2t-hla-argos985-mycob140.tar](https://objectstorage.uk-london-1.oraclecloud.com/n/lrbvkel2wjot/b/human-genome-bucket/o/human-t2t-hla-argos985-mycob140.tar) | 2023-07 |



## Install  [![Install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat-square&logo=anaconda)](https://biocontainers.pro/tools/hostile) [![Install with Docker](https://img.shields.io/badge/install%20with-docker-important.svg?style=flat-square&logo=docker)](https://biocontainers.pro/tools/hostile)

Installation with conda/mamba or Docker is recommended due to non-Python dependencies (Bowtie2, Minimap2, Samtools and Bedtools). Hostile is tested with Ubuntu Linux 22.04, MacOS 12, and under WSL for Windows.

**Conda/mamba**

```bash
conda create -y -n hostile -c conda-forge -c bioconda hostile
conda activate hostile
```

**Docker**

```bash
docker run quay.io/biocontainers/hostile:0.2.0--pyhdfd78af_0

# Build your own
wget https://raw.githubusercontent.com/bede/hostile/main/Dockerfile
docker build . --platform linux/amd64
```

**Development install**

```bash
git clone https://github.com/bede/hostile.git
cd hostile
conda env create -f environment.yml  # Mamba/Micromamba are faster
conda activate hostile
pip install --editable '.[dev]'
pytest
```



## Command line usage

![carbon](screenshot.png)

```bash
$ hostile clean --help
usage: hostile clean [-h] --fastq1 FASTQ1 [--fastq2 FASTQ2] [--aligner {bowtie2,minimap2,auto}] [--index INDEX] [--rename] [--sort-by-name] [--out-dir OUT_DIR] [--threads THREADS]
                     [--aligner-args ALIGNER_ARGS] [--force] [--debug]

Remove reads aligning to a target genome from fastq[.gz] input files

options:
  -h, --help            show this help message and exit
  --fastq1 FASTQ1       path to forward fastq.gz] file
  --fastq2 FASTQ2       optional path to reverse fastq[.gz] file
                        (default: None)
  --aligner {bowtie2,minimap2,auto}
                        alignment algorithm. Use Bowtie2 for short reads and Minimap2 for long reads
                        (default: auto)
  --index INDEX         path to custom genome or index. For Bowtie2, provide an index without .bt2 extension
                        (default: None)
  --rename              replace read names with incrementing integers
                        (default: False)
  --sort-by-name        sort reads by name (before renaming, if enabled)
                        (default: False)
  --out-dir OUT_DIR     path to output directory
                        (default: ./)
  --threads THREADS     number of threads to use
                        (default: 10)
  --aligner-args ALIGNER_ARGS
                        additional arguments for alignment
                        (default: )
  --force               overwrite existing output files
                        (default: False)
  --debug               show debug messages
                        (default: False)
```



**Short reads**

```bash
$ hostile clean --fastq1 reads.r1.fastq.gz --fastq2 reads.r2.fastq.gz
INFO: Using Bowtie2 (paired reads)
INFO: Found cached index (/Users/bede/Library/Application Support/hostile/human-t2t-hla)
INFO: Cleaning…
INFO: Complete
[
    {
        "aligner": "bowtie2",
        "index": "/path/to/data/dir/human-t2t-hla",
        "fastq1_in_name": "reads.r1.fastq.gz",
        "fastq2_in_name": "reads.r2.fastq.gz",
        "fastq1_in_path": "/path/to/hostile/reads.r1.fastq.gz",
        "fastq2_in_path": "/path/to/hostile/reads.r2.fastq.gz",
        "fastq1_out_name": "reads.r1.clean_1.fastq.gz",
        "fastq2_out_name": "reads.r2.clean_2.fastq.gz",
        "fastq1_out_path": "/path/to/hostile/reads.r1.clean_1.fastq.gz",
        "fastq2_out_path": "/path/to/hostile/reads.r2.clean_2.fastq.gz",
        "reads_in": 20,
        "reads_out": 20,
        "reads_removed": 0,
        "reads_removed_proportion": 0.0
    }
]
```

```bash
$ hostile clean --rename --fastq1 reads_1.fastq.gz --fastq2 reads_2.fastq.gz \
  --index /path/to/human-t2t-hla-argos985-mycob140 > decontamination-log.json
INFO: Using Bowtie2
INFO: Found cached index (/Users/bede/Library/Application Support/hostile/human-t2t-hla)
INFO: Cleaning…
INFO: Complete
```



**Long reads**

```bash
$ hostile clean --fastq1 tests/data/h37rv_10.r1.fastq.gz
INFO: Using Minimap2's long read preset (map-ont)
INFO: Found cached index (/Users/bede/Library/Application Support/hostile/human-t2t-hla)
INFO: Cleaning…
INFO: Complete
[
    {
        "aligner": "minimap2",
        "index": "/Users/bede/Library/Application Support/hostile/human-t2t-hla.fa.gz",
        "fastq1_in_name": "reads.fastq.gz",
        "fastq1_in_path": "/path/to/hostile/reads.fastq.gz",
        "fastq1_out_name": "reads.clean.fastq.gz",
        "fastq1_out_path": "/path/to/hostile/reads.clean.fastq.gz",
        "reads_in": 10,
        "reads_out": 10,
        "reads_removed": 0,
        "reads_removed_proportion": 0.0
    }
]
```



## Python usage

```python
from pathlib import Path
from hostile.lib import clean_paired_fastqs, ALIGNER

# Long reads, defaults
clean_fastqs(
    fastqs=[Path("reads.fastq.gz")],
)

# Paired short reads, all the options, capture log
log = lib.clean_paired_fastqs(
    fastqs=[(Path("reads_1.fastq.gz"), Path("reads_2.fastq.gz"))],
    aligner=ALIGNER.minimap2,
    index=Path("reference.fasta.gz"),
    out_dir=Path("decontaminated-reads"),
    force=True,
    threads=4
)

print(log)
```



## Masking reference genomes

The `mask` subcommand makes it easy to create custom-masked reference genomes and achieve maximum retention of specific target organisms:
```bash
hostile mask human.fasta lots-of-bacterial-genomes.fasta --threads 8
```
You may wish to use one of the existing [reference genomes](#reference-genomes) as a starting point. Masking uses Minimap2's `asm10` preset to align the supplied target genomes with the reference genome, and bedtools to mask out all aligned regions. For Bowtie2—the default aligner for decontaminating short reads—you will also need to build an index before you can use your masked genome with Hostile.

```bash
bowtie2-build masked.fasta masked-index
hostile clean --index masked-index --fastq1 reads_1.fastq.gz --fastq2 reads_2.fastq.gz
```



## Citation

[BioRxiv preprint](https://www.biorxiv.org/content/10.1101/2023.07.04.547735) (accepted for publication in Oxford Bioinformatics)

```latex
@article {Constantinides2023,
	author = {Bede Constantinides and Martin Hunt and Derrick W Crook},
	title = {Hostile: accurate host decontamination of microbial sequences},
	elocation-id = {2023.07.04.547735},
	year = {2023},
	doi = {10.1101/2023.07.04.547735},
	publisher = {Cold Spring Harbor Laboratory},
	URL = {https://www.biorxiv.org/content/early/2023/07/21/2023.07.04.547735},
	eprint = {https://www.biorxiv.org/content/early/2023/07/21/2023.07.04.547735.full.pdf},
	journal = {bioRxiv}
}
```
