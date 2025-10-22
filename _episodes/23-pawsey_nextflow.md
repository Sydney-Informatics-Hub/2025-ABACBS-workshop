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
### Load required modules
Pawsey has pre-installed Nextflow and Singularity which can be loaded in your user environment using

```bash
module load module load nextflow/24.10.0 #TODO: test this (prefer not conda installed)
module load singularity/4.1.0-slurm
```

### Test required modules

To determine if the Nextflow and Singularity modules has been successfully loaded, you can run the following.

```bash
module list
```

This command displays all currently loaded modules. If `nextflow/24.10.0` and `singularity/4.1.0-slurm` appears in the output,
the module has been successfully loaded into your environment.


### Nextflow workflow execution
During workflow execution Nextflow will pull the necessary containers from a hosted repository.

```bash
nextflow run nf-core/hello
```

You can see the workflow code that has been pulled is available at `$HOME/.nextflow/assets/nf-core/hello`. Similarly, NextFlow will automatically pull the containers required to execute the workflow. These can be found in the default work directory.

```bash
ls work/
```

It can be impractical to re-build images each time a pipeline is executed. It can make sense to store images in a shared location to avoid redundant downloads and building.

```
export SINGULARITY_CACHEDIR
export SINGULARITY_LIBRARYDIR
export NXF_SINGULARITY_CACHEDIR
export NXF_SINGULARITY_LIBRARYDIR
```

# TODO: Any extra background here?