---
title: "Run NextFlow pipeline on Setonix"
teaching: 0
exercises: 5
questions:
objectives:
  - Run NextFlow pipeline on Setonix
keypoints:
  - Workflows are pulled to the $HOME directory
  - Intermediate work files are available in work/
  - Required images are pulled to work directory but can be stored in a shared cache to avoid duplication
---

> ## Disclaimer
>  Nextflow is a powerful and feature-rich workflow management system that cannot be fully covered in a short session. In this overview, we’ll focus on key concepts and practical examples. For deeper learning, please refer to the official [Nextflow documentation](http://nextflow.io/docs/latest/) and [training](https://training.nextflow.io/latest/).
{: .keypoints}


>## What is "nextflow"?
> Nextflow DSL2, based on Groovy, lets you take your analysis code and easily wrap it into a modular, automated pipeline. It helps bioinformaticians build reproducible workflows that run anywhere.
>
> **Why Use Nextflow?**
>
> - **Portable**: Same workflow can run on your laptop or HPC Setonix (Slurm).
> - **Parallel execution**: Automatically schedules tasks across available compute nodes.
> - **Reproducibility**: Uses containers or environments for consistent results.
> - **Integration with HPC schedulers**: Supports Slurm, Pawsey job scheduling system.
>
>**Core Concepts**
>
>A nextflow pipeline consists of three primary components:
>
> - **Processes** define what to run. Each process can use any Linux-compatible language (e.g., Bash, Python, R, Perl). Processes in are executed independently (i.e., they do not share a common writable state) as **tasks** and can run in parallel, allowing for efficient utilisation of computing resources
> - **Channels** define how data flows between processes. Channels asynchronously carry data between processes and can fan-out (parallel tasks) or fan-in (merge results).
>  - **Workflows** Define the order in which processes connect. They orchestrate execution, specifying dependencies and the overall structure of the pipeline.
>
{: .prereq}

### Running a nextflow workflow

Today we’ll run a simple Nextflow workflow developed to demonstrate a [nextflow template](https://github.com/Sydney-Informatics-Hub/template-nf) at the university of Sydney. 
This demo workflow shows basic Nextflow functionality through a simple genomics workflow.

> ## Demo Scenario (Brief)
> This demo simulates the start of a larger workflow where samples need to be grouped by sequencing platform for downstream processing. It uses a single samplesheet (assets/samplesheet.csv) with sample names, FASTQ paths, and platform info (e.g., Illumina or PacBio).
> The workflow runs three processes:
> 
> - check_input – Validates the samplesheet using a custom script in bin/.
> - group_samples – Splits samples into platform-specific sheets.
> - generate_report – Summarizes each group in parallel.
> 
> [TODO] add figure of workflow
> 
> 
> Outputs include validated and grouped samplesheets plus summary reports, illustrating how Nextflow handles input validation, data splitting, and parallel execution.
{: .callout}


Use git to clone the workflow code base to your working directory:

```bash
git clone https://github.com/Sydney-Informatics-Hub/template-nf-demo
```

now move into the cloned directory and look at the file structure

```
cd template-nf-demo/
tree
```

you should see something like this

```
.
├── assets
│   └── samplesheet.csv
├── bin
│   └── samplesheetchecker.sh
├── config
│   ├── gadi.config
│   ├── nimbus.config
│   ├── setonix.config
│   └── standard.config
├── LICENSE
├── main.nf
├── modules
│   ├── check_input.nf
│   ├── generate_report.nf
│   ├── group_samples.nf
│   └── template_process.nf
├── nextflow.config
└── README.md
```

>##  Files and Directories overview
>The template’s code repository is organised into a number of files and directories. Hidden directories prefixed with a . can be ignored for now, they are useful for configuring git and github and aren’t related to running your workflow. The code used in the demo workflow are:
> 
> - **main.nf**: the primary execution script, it contains workflow structure, processes, and channels. i.e. It defines your processes (the individual analysis steps) and how data flows between them.
> - **nextflow.config**: the configuration file, it contains a number of property definitions that are used by the pipeline. It specifies runtime settings such as executor, resource limits (CPU, memory), container or Conda environments, and parameter defaults.
> - **conf/** – For organising multiple configuration profiles. You can have multiple config files (e.g., `base.config`, `hpc.config` etc.) for different environments..
> - **assets/**: stores auxillary files. We’ve stored our example samplesheet.csv here.
> - **bin/**: stores custom scripts to be executed by Nextflow processes. We’ve stored a custom script samplesheetchecker.sh run by the first process of this workflow here.
> - **modules/**: contains code run by each process executed by the workflow. Processes have been separated into different .nf files for the sake of readability and easy maintenance.
{: .callout}

Pawsey has pre-installed Nextflow and Singularity which can be loaded in your user environment using

```
module load nextflow/24.10.0 singularity/4.1.0-slurm
```

run the pipeline

```bash
nextflow run main.nf --input assets/samplesheet.csv
```


> ## Containers in Nextflow Workflows
> Our demo doesn’t use containers, but they are one of the most effective ways to manage software in workflow development, sespecially with Nextflow.
>
> **Why containers?**
>
> A container is a lightweight, portable environment that bundles an application together with everything it needs to run such as libraries, dependencies, and system tools. It is a simple and reliable alternative to installing software directly on your system or HPC environment (which often requires managing dependencies manually).
> On HPC systems, this means isolation from other environments, reproducibility across platforms, and simplified maintenance without manual installs or dependency troubleshooting. 
>
> It is recommended to pull containers from trusted sources like [BioContainers](https://biocontainers.pro/), [quay.io](quay.io) or [Seqera](https://seqera.io/containers/). During execution, Nextflow automatically pulls required images, often from these repositories, and stores them in the work directory.
> 
> **Tip:** Rebuilding or downloading containers every run is inefficient. Cache them in a shared location to save time and bandwidth:
> 
> ```bash 
> export SINGULARITY_CACHEDIR=/path/to/cache
> export SINGULARITY_LIBRARYDIR=/path/to/library
> export NXF_SINGULARITY_CACHEDIR=/path/to/cache
> export NXF_SINGULARITY_LIBRARYDIR=/path/to/libraryShow more lines
> ```
{: .keypoints}

# TODO: Any extra background here?
