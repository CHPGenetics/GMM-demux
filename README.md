# GMM-Demux 
A Gaussian Mixture Model based software for processing cell hashing data.

## Description
GMM-Demux removes Multi-Sample-Multiplets (MSMs) in a cell hashing dataset and estimates the fraction of Same-Sample-Multiplets (SSMs) and singlets in the remaining dataset.

# Author
 Hongyi Xin, Qi Yan, Yale Jiang, Jiadi Luo, Carla Erb, Richard Duerr, Kong Chen* and Wei Chen*

# Maintainer
Hongyi Xin <xhongyi@pitt.edu>


## Usage
```bash
python3 GMM-demux.py <cell_hashing_path> <HTO_names> <estimated_cell_num>
```
MSM-free droplets are stored in *GMM_Demux_mtx* by default.

## Example Usage
```bash
python3 GMM-demux.py example_input/outs/filtered_feature_bc_matrix HTO_1,HTO_2,HTO_3,HTO_4 35685
```
