# GMM-Demux 
A Gaussian Mixture Model based software for processing cell hashing data.

Below shows an example classification result. Orange dots are multi-sample multiplets.

<img src="GMM_simplified.png" alt="GMM-Demux example" width="600"/>

## Description
GMM-Demux removes Multi-Sample-Multiplets (MSMs) in a cell hashing dataset and estimates the fraction of Same-Sample-Multiplets (SSMs) and singlets in the remaining dataset.
GMM-Demux also verifies if a putative cell type exists, or is it merely an artifact induced by multiplets.

Multiplet-induced fake cell types are called "phony cell types".

An example phony cell type in a CITE-seq dataset is provided in the picture below:

<img src="phony.png" width="600"/>

In the above figure, both CD3+CD19+ and CD4+CD8+ cell types are multiplet induced cell types.

Phony type clusters have large percentages of MSMs, as above figure shows. Both phony type clusters have large MSM percentages.

Percentages of MSMs are used as key features by GMM-Demux to classify GEM clusters.

## Features
* Remove cell-hashing-identifiable multiplets from the dataset.
* Estimate the fraction of cell-hashing-unidentifiable multiplets in the remaining dataset (the RSSM value).
* Tests if a putative cell type is a pure (real) cell type or is it a phony cell type.

## Example Dataset
* An example cell hashing data is provided in the *example_input* folder. It contains the per drop HTO count matrix of a 4-sample cell hashing library prep. The input folder has the same file format with the CellRanger v3 output.

# Authors
 Hongyi Xin, Qi Yan, Yale Jiang, Jiadi Luo, Carla Erb, Richard Duerr, Kong Chen* and Wei Chen*

# Maintainer
Hongyi Xin <gohongyi at gmail.edu>

## Requirement

GMM-Demux requires python3 (>3.5).

## Install

GMM-Demux can be directly installed from PyPi. Or it can be built and installed locally.

* Install GMM-Demux from PyPi.
```bash
pip3 install --user GMM_Demux
```
If choose to install from PyPi, it is unnecessary to download GMM-Demux from github. However, we still recommend downloading the example dataset to try out GMM-Demux.

* Install GMM-Demux locally using [setuptools](https://packaging.python.org/tutorials/installing-packages/) and pip3.
```bash
cd <GMM-Demux dir>
python3 setup.py sdist bdist_wheel
pip3 install --user . 
```

* Post installation processes

If this is the first time you install a python3 software through pip, make sure you add the pip binary folder to your `PATH` variable.
Typically, the pip binary folder is located at ```~/.local/bin```.

To temporarily add the pip binary folder, run the following command:
```bash
export PATH=~/.local/bin:$PATH
```

To permenantly add the pip library folder to your `PATH` variable, append the following line to your `.bashrc` file.
```bash
PATH=~/.local/bin:$PATH
```

## Content

The source code of GMM-Demux is supplied in the ```GMM_Demux``` folder.

An example cell hashing dataset is also provided, located in the ```example_input/outs/filtered_feature_bc_matrix``` folder.

An example set of hand-curated putative cell types are provided in the ```example_cell_types''' folder.

An example csv HTO file of the above cell hashing data is provided as the ```example_hto.csv''' file.

## Usage

### Case 1: Basic Usage
Once installed, GMM-Demux is directly accessible with the ```GMM-demux``` command.
```bash
GMM-demux <cell_hashing_path> <HTO_names>
```

```<HTO_names>``` is a list of strings separated by ',' without whitespace.
For example, there are four HTO tags in the example cell hashing dataset supplied in this repository.
They are **HTO_1**, **HTO_2**, **HTO_3**, **HTO_4**. The ```<HTO_names>``` variable therefore is ```HTO_1,HTO_2,_HTO_3,HTO_4```.

MSM-free droplets are stored in folder *GMM_Demux_mtx* under the current directory by default.
The output path can also be specified through the `-o` flag.

#### Example Command 
An example cell hashing data is provided in *example_input*. <HTO_names> can be obtained from the features.tsv file.
```bash
GMM-demux example_input/outs/filtered_feature_bc_matrix HTO_1,HTO_2,HTO_3,HTO_4
```

<HTO_names> are obtained from the features.tsv file. The feature.tsv file of the example cell hashing dataset is shown below.

![HTO names example](features.png)

#### Output
* CellRanger MSM-free drops, in MTX format. Compatible with CellRanger 3.0.

### Case 2: Compute the MSM and SSM rates
To compute the MSM and SSM rates, GMM-Demux requires the -u flag:

* -u SUMMARY, --summary SUMMARY Generate the statstic summary of the dataset. Requires an estimated total number of cells in the assay as input.
 
-u flag requires an additional <NUM_OF_CELL> argument, which is the estimated total count of cells in the single cell assay.

#### Example Command
```bash
GMM-demux example_input/outs/filtered_feature_bc_matrix HTO_1,HTO_2,HTO_3,HTO_4 -u 35685
```

#### Output
Below is an example report:
![Summary example](summary.png)

* MSM denotes the percentage of identified and removed multiplets among all droplets.
* SSM denotes the percentage of unidentifiable multiplets among all droplets.
* RSSM denotes the percentage of multiplets among the output droplets (after removing identifiable multiplets). RSSM **measures the quality of the cell hashing dataset**.

### Case 3: Verify if a cell type exists 
GMM-Demux verifies a putative cell type with the -e flag:

* -e EXAMINE, --examine EXAMINE Provide the cell list. Requires a file argument. Only executes if -u is set.

-e flag requires a file name, which stores the list of droplet barcodes of the putative cell type.

#### Example Command
```bash
GMM-demux example_input/outs/filtered_feature_bc_matrix HTO_1,HTO_2,HTO_3,HTO_4 -u 35685 example_cell_types/CD19+.txt
GMM-demux example_input/outs/filtered_feature_bc_matrix HTO_1,HTO_2,HTO_3,HTO_4 -u 35685 example_cell_types/Doublets/CD3+CD4+CD19+.txt
```

#### Output
An example output of a pure cell type:
![Pure type example](pure_type.png)

An example output of a phony cell type:
![Phone type example](phony_type.png)

### Case 4: Use the csv file format as input, instead of the mtx format 
#### Example Command
```bash
GMM-demux -c example_hto.csv HTO_1,HTO_2,HTO_3,HTO_4 -u 35685
```

## Optional Arguments
* -h: show help information.
* -f FULL, --full FULL  Generate the full classification report. Require a path argument.
* -s SIMPLIFIED, --simplified SIMPLIFIED  Generate the simplified classification report. Require a path argument.
* -o OUTPUT, --output OUTPUT  The path for storing the Same-Sample-Droplets (SSDs). SSDs are stored in mtx format. Requires a path argument. Default path: SSD_mtx.
* -r REPORT, --report REPORT  Specify the file to store summary report. Require a file argument.
* -c CSV, --csv  Take input in csv format, instead of mmx format.
* -x SKIP, --skip FULL\_REPORT  Load a full classification report and skip the mtx folder as input. Require a path argument.
* -a AMBIGUOUS, --ambiguous AMBIGUOUS  The estimated chance of having a phony GEM getting included in a pure type GEM cluster by the clustering algorithm. Requires a float in (0, 1). Default value: 0.05. Only executes if -e executes.
* -t THRESHOLD, --threshold THRESHOLD  Provide the confidence threshold value. Requires a float in (0,1). Default value: 0.8.

## Parsing the Classification Output
There are two files in a classification output folder. A config file (ending with .config) and a classification file (ending with .csv).

The classification file contains the label of each droplet as well as the probability of the classification. The classification is represented with numbers which are explained in the config file.

Below shows the classification output of the example data:

<img src="class_output.png" width="600"/>
 
## Online Cell Hashing Experiment Planner
A GMM-Demux based online cell hashing experiment planner is publically accessible at [here](https://www.pitt.edu/~wec47/gmmdemux.html).

[<img src="planner.png" alt="Online explanner example" width="600"/>](https://www.pitt.edu/~wec47/gmmdemux.html)

## Citation
If you find this code useful in your research, please consider citing:

    @article{xin2019sample,
      title={Sample demultiplexing, multiplet detection, experiment planning and novel cell type verification in single cell sequencing},
      author={Xin, Hongyi and Yan, Qi and Jiang, Yale and Lian, Qiuyu and Luo, Jiadi and Erb, Carla and Duerr, Richard and Chen, Kong and Chen, Wei},
      journal={bioRxiv},
      pages={828483},
      year={2019},
      publisher={Cold Spring Harbor Laboratory}
    }
