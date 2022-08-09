# OBITools workflow

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.6977255.svg)](https://doi.org/10.5281/zenodo.6977255)


## About

This is a snakemake workflow based on the obitools suite of programs, that analyzes DNA metabarcoding data.

Sequence analysis is performed with the obitools (Boyer et al. 2016) and sumaclust (Mercier et al. 2013) through a Snakemake pipeline (Molder et al. 2021).


## Getting started

### Prerequisites

This workflow is meant to be executed on a computing cluster running with **SLURM**. It has been written to run on the Genotoul computing cluster (http://bioinfo.genotoul.fr/).

### Installation

Clone the repository:
```sh
git clone https://github.com/AnneSoBen/obitools_workflow.git
```
### Directories and files structure

The repository contains five folders:
- `config/`: contains the configuration file of the Snakemake workflow (`config.yaml`). This is where the value of the options for the various commands used is defined.
- `log/`: where log files of each rule are written.
- `resources/`: where you should download/copy your raw data (cf. _Download your data_)
- `results/`: where all output files are written.
- `workflow/`: contains the Snakemake workflow (`Snakefile`), the configuration file of the submission parameters on the cluster (`cluster.yaml`), the script to submit the workflow on the cluster (`sub_smk.sh`). 

### Download your data

Download/copy your data in the `resources/` folder. Three files are required:
- forward and reverse fastq files
- the corresponding ngsfilter file

They should be named as follows: `prefix_R1.fastq`, `prefix_R2.fastq`, `prefix_ngsfilter.tab`

And be put in a subfolder whose name is the prefix of the files (see _Example_).


## Usage

Before running the workflow, the two configuration files have to be modified: `workflow/cluster.yaml` that sets up the ressources available for each rule, and `config/config.yaml` where you can edit the values of the parameters used by the rules and the basename of your files.

Then, to run the workflow in a single command on the cluster:

```sh
cd workflow
sbatch sub_smk.sh
```

## Example

### Download toy data

If you want to test the workflow, download toy data from the obitools tutorial (https://pythonhosted.org/OBITools/wolves.html) in the `resources/` folder:
```sh
wget -O resources/wolf_tutorial.zip https://pythonhosted.org/OBITools/_downloads/wolf_tutorial.zip
unzip resources/wolf_tutorial.zip -d resources/
mv resources/wolf_tutorial resources/wolf_diet
rm resources/wolf_tutorial.zip
```
Rename the files to fit the template decribed above (or create symbolic links):
```sh
ln -s wolf_F.fastq resources/wolf_diet/wolf_diet_R1.fastq
ln -s wolf_R.fastq resources/wolf_diet/wolf_diet_R2.fastq
ln -s wolf_diet_ngsfilter.txt resources/wolf_diet/wolf_diet_ngsfilter.tab
```
You should get this directories and files structure:
```sh
tree
```

```
.
├── config
│   └── config.yaml
├── LICENSE
├── log
├── README.md
├── resources
│   └── wolf_diet
│       ├── db_v05_r117.fasta
│       ├── embl_r117.ndx
│       ├── embl_r117.rdx
│       ├── embl_r117.tdx
│       ├── wolf_diet_ngsfilter.tab -> wolf_diet_ngsfilter.txt
│       ├── wolf_diet_ngsfilter.txt
│       ├── wolf_diet_R1.fastq -> wolf_F.fastq
│       ├── wolf_diet_R2.fastq -> wolf_R.fastq
│       ├── wolf_F.fastq
│       └── wolf_R.fastq
├── results
└── workflow
    ├── cluster.yaml
    ├── Snakefile
    └── sub_smk.sh
```

Note that the name of the subfolder containing your source files (fastq and ngsfilter files) should be the prefix of the files.

The config.yaml file is already modified to fit this data.

### Run the workflow

Now run the workflow on the cluster:
```sh
cd workflow/
sbatch sub_smk.sh
```

### Option: merging libraries

You may want to merge libraries, for example if technical replicates are splitted in different libraries. To allow this, the value of "tomerge" in the `config/config.yaml` file should be set to `TRUE`. Besides, the prefix of your library files should be listed in the `config/config.yaml` file, such as:

```
tomerge:
  TRUE
resourcesfolder:
  ../resources/
resultsfolder:
  ../results/
fastqfiles:
  - myfirstlibfileprefix
  - mysecondlibfileprefix
mergedfile:
  mymergedlibs
```

The source files of each library should be in separate subfolders. For example:

```
└─ resources
   └── myfirstlibprefix
   |   ├── myfirstlibprefix_ngsfilter.tab
   |   ├── myfirstlibprefix_R1.fastq
   |   └── myfirstlibprefix_R2.fastq
   └── mysecondlibprefix
       ├── mysecondlibprefix_ngsfilter.tab
       ├── mysecondlibprefix_R1.fastq
       └── mysecondlibprefix_R2.fastq
```

Two ngsfilter files will be necessary: `resources/myfirstlibfileprefix/myfirstlibfileprefix_ngsfilter.tab` and `resources/myfirstlibfileprefix/mysecondlibfileprefix_ngsfilter.tab`.

:warning: If you want to be able to distinguish your technical replicates in the final output, don't forget to give your samples different names in the ngsfilter files, e.g. for a sample named "sample", you could change its name to "sample_a" in the first ngsfilter file and "sample_b" in the second ngsfilter file (if you have to technical replicates).

The value of "mergedfile" corresponds to the prefix of the merged files from the dereplication to the end of the workflow.


## Going further

You may want to clean up potential molecular artifacts: have a look at the R package [metabaR](https://github.com/metabaRfactory/metabaR)!


## Acknowledgements

Thanks to **[Lucie Zinger](https://luciezinger.wordpress.com/)**, **[Frédéric Boyer](https://www.researchgate.net/profile/Frederic-Boyer-3)**, **[Céline Mercier](https://www.celine-mercier.info/)** and **Clément Lionnet** for their help with the obitools!


## How to cite this repository

Anne-Sophie Benoiston. (2022). AnneSoBen/obitools_workflow: v1.0.1. GitHub. https://doi.org/10.5281/zenodo.6977255.

:triangular_flag_on_post: Don't forget to cite this repository is you use if for your research :slightly_smiling_face:


## References

Boyer, F., Mercier, C., Bonin, A., Bras, Y. L., Taberlet, P., & Coissac, E. (2016). obitools : A unix-inspired software package for DNA metabarcoding. Molecular Ecology Resources, 16(1), 176‑182.

Mercier, C., Boyer, F., Bonin, A., & Coissac, E. (2013, November). SUMATRA and SUMACLUST: fast and exact comparison and clustering of sequences. In Programs and Abstracts of the SeqBio 2013 workshop. Abstract (pp. 27-29).

Mölder, F., Jablonski, K. P., Letcher, B., Hall, M. B., Tomkins-Tinch, C. H., Sochat, V., ... & Köster, J. (2021). Sustainable data analysis with Snakemake. F1000Research, 10.

Zinger, L., Lionnet, C., Benoiston, A. S., Donald, J., Mercier, C., & Boyer, F. (2021). metabaR: an R package for the evaluation and improvement of DNA metabarcoding data quality. Methods in Ecology and Evolution, 12(4), 586-592.
