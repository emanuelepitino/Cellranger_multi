# Cellranger_multi
In this repository I will show how to run cell ranger multi (GEX and TCR) when having multiple subprojects into a single one (i.e. SCGRES_90_91)

1. Pre-initialize directory
The first step will be to create a folder (es 1-Cellranger) with inside scripts, results, and data folders.
Inside results we will have filtered_feature_bc_matrix, metrics_summaries and web_summaries.

```console
mkdir -p 1-Cellranger scripts results data
mkdir -p results filtered_feature_bc_matrix metrics_summaries web_summaries
```
2. Get the info of the subproject by running 1-get_info.sh
This script takes the subproject information from the lims and, as for the moment i am writing this, **it can only be run from login6 node**
If we have two different subprojects merged, we will run it separately and then merge the info files

```console
bash 1-get_info.sh SCGRES_90
bash 1-get_info.sh SCGRES_91
```
