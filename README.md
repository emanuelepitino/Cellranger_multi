# Cellranger_multi
In this repository I will show how to run cell ranger multi (GEX and TCR) when having multiple subprojects into a single one (i.e. SCGRES_90_91)

## *1. Change paths*
Before starting, we need to change the paths of our project and subproject in config.py, which we will later use inside 3-init.py.

We update the project and subproject variables by running 0-update_config.cmd, passing as arguments project and subproject names.

Please check config.py and change the main paths according to your project directory structure.

```console
bash 0-update_config.cmd NSCLC SCGRES_90_91
```

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
### 2.2 Merge output
The output will be two different info files. We need to merge them together and to rename the subproject column to "SCGRES_90_91":

```console
cat info_SCGRES_90.txt > info.txt
tail -n +2 info_SCGRES_91.txt >> info.txt
awk -F'\t' 'NR==1 {print; next} {OFS=FS; $2="SCGRES_90_91"; print}' info.txt > temp && mv temp info.txt
```

### 2.3 Filter
Now we need to filter for **LanePassFail == pass** and **libraryPassFail == pass**:

```console
awk -F'\t' 'NR==1 || ($13=="pass" && $14=="pass")' info.txt > temp && mv temp info.txt
```

## *3. Create the metadata*

For cellranger multi we need the metadata file, which we will obtain from the info.txt (lims) running the script 2-Create_metadata.R.
This script launch a job to execute "2-Create_metadata.R".

```console
sbatch 9-Create_metadata.cmd
```

## *4. Get the fastq paths*

Now we want to get the symlink to the fastq files related to our project. We do so by runnig "2-fastqs_path.py".
You need to load a python environment with Numpy. This script takes as arguments the info.txt file and the subproject name.

```console
module load epitino_py_3.7
python 2-fastqs_path.py --info info.txt --subproject SCGRES_90_91
```
## *5. Initialize jobs directory*

Now we have everything to initialize and create the sbatch jobs to run cellranger multi.
We run "3-init.py" with the following arguments: metadata, reference (human,mouse), info_file.

```console
python 3-init.py --info_file info.txt --metadata metadata.csv --reference human
```
## *6. Run cellranger*

We run all the jobs by calling 4-run_jobs.cmd

```console
bash 4-run_jobs.cmd
```

## *7. Copy output*

We copy the output of cellranger by running the following:

```console
bash 6-Copy_filtered_matrices.cmd
bash 7-Copy_metrics_matrices.cmd
bash 8-Copy_control_reports.cmd
```










