# Cellranger_multi
In this repository I will show how to run cell ranger multi (GEX and TCR) when having multiple subprojects into a single one (i.e. SCGRES_90_91)

## **1. Pre-initialize directory**
The first step will be to create a folder (es 1-Cellranger) with inside scripts, results, and data folders.
Inside results we will have filtered_feature_bc_matrix, metrics_summaries and web_summaries.

```console
mkdir -p 1-Cellranger/{scripts,results,data}
mkdir -p 1-Cellranger/results/{filtered_feature_bc_matrix,metrics_summaries,web_summaries}
cd 1-Cellranger
```
## **2. Get the info of the subproject**

This script 1-get_info.sh takes the subproject information from the lims and, as for the moment i am writing this,we need to:

A. **Run it only from login6 node**

B. **Run it outside of an interactive session**

### 2.1 Run 1-get_info.sh
If we have two different subprojects merged, we will run it separately and then merge the info files.

```console
cd scripts
bash 1-get_info.sh SCGRES_90
bash 1-get_info.sh SCGRES_91
```
### 2.2 The output will be two different info files. We need to merge them together and to rename the subproject column to "SCGRES_90_91":

```console
cat info_SCGRES_90.txt > info.txt
tail -n +2 info_SCGRES_91.txt >> info.txt
awk -F'\t' 'NR==1 {print; next} {OFS=FS; $2="SCGRES_90_91"; print}' info.txt > temp && mv temp info.txt
```

### 2.3 Now we need to filter for **LanePassFail == pass** and **libraryPassFail == pass**:

```console
awk -F'\t' '$13=="pass" && $14=="pass"' info.txt  > info.txt
```




