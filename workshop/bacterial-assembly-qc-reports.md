# Bacterial assembly QC report

This is a walkthrough demonstrating how to generate a bacterial assembly QC report on the Terra platform. Prior to running the workflow to generate this report, you will have run the `TheiaProk_Illumina_PE` workflow, which performs de novo assembly and characterization.

## Description of the workflow

The `bacterial_assembly_qc_report` workflow is used to generate quality control (QC) visualizations for bacterial sequencing data. We rely on relevant QC metrics — estimated coverage, number of contigs, and assembly length — to assess the sequencing quality of the different samples. The visualizations are created using Plotly to allow for interactive charts and graphs, which are then compiled into an HTML report. No server is required to access the report — simply download the output HTML and open in your browser of choice.

## What does it require as input?
This workflow has both required and optional inputs, and requires that the samples to be visualized be grouped into a set. If you do not have an existing set, please follow the following steps to create one. Otherwise, select your set of interest in steps 1 and 2 of the workflow launch.

*Step 1 of launching the workflow, select the set option
*Step 2 (Select Data), select the `Create a new set from selected table` option, choose your samples to be included in the new set, and name the set
The workflow will fail if the samples are selected invidually and not in a set. Please ensure this before moving forward with the inputs.

Required inputs:
`sample_ids`: A list of sample IDs for which the QC visualizations will be generated. To retrieve this from your workspace, use the format "this.{data_table}.{sample_name}". For example — if your data table was named "broad_demos" and your ids were named "sample_id", you'd want to pass in "this.broad_demos.sample_id"
`workspace_name`: The name of the Terra workspace where the data resides.
`workspace_project`: The name of the billing project associated with the workspace.
`input_table_name`: The name of the Terra data table from which the input data is selected. This is not the table with sets — it is the original table where the data lives that the selected set is referencing.

Optional inputs:
`grouping_column_name` (default "gambit_predicted_taxon"): The name of the column used for grouping/coloring in the visualizations, typically representing the predicted organism taxon.

`docker`: The Docker image to be used for running the task, which contains the necessary environment and scripts for generating the visualizations. No need to modify — this is just for development purposes.

custom threshold inputs for various QC metrics (`custom_est_coverage_thresholds`, `custom_contig_thresholds`, `custom_assembly_thresholds`, `custom_mean_q_thresholds`): JSON strings specifying custom thresholds for the respective QC metrics. The following thresholds are included in the workflow, each broken down by genus:
