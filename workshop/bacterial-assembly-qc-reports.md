# Bacterial assembly QC report

This is a walkthrough demonstrating how to generate a bacterial assembly QC report on the Terra platform. Prior to running the workflow to generate this report, you will have run the `TheiaProk_Illumina_PE` workflow, which performs de novo assembly and characterization.

# Contents
{:.no_toc}

1. Table of Contents
{:toc}

## Description of the workflow

The `bacterial_assembly_qc_report` workflow is used to generate quality control (QC) visualizations for bacterial sequencing data. We rely on relevant QC metrics — estimated coverage, number of contigs, and assembly length — to assess the sequencing quality of the different samples. 

The visualizations are created using Plotly to allow for interactive charts and graphs, which are then compiled into an HTML report. No server is required to access the report — simply download the output HTML and open in your browser of choice.

## What does it require as input?
This workflow has both required and optional inputs, and requires that the samples to be visualized be grouped into a set. If you do not have an existing set, please follow the following steps to create one. Otherwise, select your set of interest in steps 1 and 2 of the workflow launch.

- Step 1 of launching the workflow, select the set option
- Step 2 (Select Data), select the `Create a new set from selected table` option, choose your samples to be included in the new set, and name the set
The workflow will fail if the samples are selected invidually and not in a set. Please ensure this before moving forward with the inputs.

*Required inputs*:
`sample_ids`: A list of sample IDs for which the QC visualizations will be generated. To retrieve this from your workspace, use the format "this.{data_table}.{sample_name}". For example — if your data table was named "broad_demos" and your ids were named "sample_id", you'd want to pass in "this.broad_demos.sample_id"
`workspace_name`: The name of the Terra workspace where the data resides.
`workspace_project`: The name of the billing project associated with the workspace.
`input_table_name`: The name of the Terra data table from which the input data is selected. This is not the table with sets — it is the original table where the data lives that the selected set is referencing.

*Optional inputs*:
`grouping_column_name` (default "gambit_predicted_taxon"): The name of the column used for grouping/coloring in the visualizations, typically representing the predicted organism taxon.

`docker`: The Docker image to be used for running the task, which contains the necessary environment and scripts for generating the visualizations. No need to modify — this is just for development purposes.

custom threshold inputs for various QC metrics (`custom_est_coverage_thresholds`, `custom_contig_thresholds`, `custom_assembly_thresholds`, `custom_mean_q_thresholds`): JSON strings specifying custom thresholds for the respective QC metrics. The following thresholds are included in the workflow, each broken down by genus:

- Estimated Coverage (`est_coverage_clean`):
Listeria: 20
Campylobacter: 20
Escherichia: 40
Salmonella: 30
Vibrio: 40

- Number of Contigs (`number_contigs`):
Listeria: 100
Campylobacter: 200
Escherichia: 600
Salmonella: 400
Vibrio: 200

- Assembly Length (`assembly_length`):
Listeria: min 2,800,000, max 3,100,000
Campylobacter: min 1,400,000, max 2,200,000
Escherichia: min 4,200,000, max 5,900,000
Salmonella: min 4,400,000, max 5,600,000
Vibrio: min 3,800,000, max 5,300,000

If you wish to override any of these thresholds or add a new treshold, you may do so by passing in a json file with the metric, genus name and the relevant value to each metric's input space. Note that the json file must be uploaded to your Terra workspace to be accessible when configuring the workflow's inputs. The metrics must be exactly defined as:

`est_coverage_clean` for estimated coverage
`number_contigs` for number of contigs
`assembly_length` for assembly length
note that the upper and lower thresholds must be marked by `min` and `max`
An example of a valid json file if we wanted to add metrics for Klevsiella and Shigella would look like:

{ "est_coverage_clean": { "Klebsiella": 17, "Shigella": 17 }, "number_contigs": { "Klebsiella": 17, "Shigella": 17 }, "assembly_length": { "Klebsiella": { "min": 1500000, "max": 2500000 }, "Shigella": { "min": 1500000, "max": 2500000 } } }

Please not that this example json file does not contain real values and is only provided for reference.

##What does it return as output?

`visualization_html`: An HTML file (QC_visualizations.html by default) containing interactive QC visualizations. This report includes various plots and tables summarizing the QC metrics for the provided samples, allowing for a comprehensive review of sequencing quality across different metrics and samples.

Example html report:
![Screenshot 2024-02-16 at 11 16 52 AM](https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/d1630e19-44a5-41cf-affc-e347da1dd834)

