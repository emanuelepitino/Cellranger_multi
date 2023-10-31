# Cellranger_multi
In this repository I will show how to run cell ranger multi (GEX and TCR) when having multiple subprojects into a single one (i.e. SCGRES_90_91)

1. Pre-initialize directory
The first step will be to create a folder (es 1-Cellranger) with inside scripts, results, and data folders.
Inside results we will have filtered_feature_bc_matrix, metrics_summaries and web_summaries.

\\\console
mkdir -p 1-Cellranger scripts results data
mkdir -p results filtered_feature_bc_matrix metrics_summaries web_summaries
\\\
