# Bacterial *de novo* assembly and characterization

This is a walkthrough demonstrating *de novo* bacterial assembly and characterization workflows on the Terra cloud platform on Illumina paired-end data.

# Contents
{:.no_toc}

1. Table of Contents
{:toc}

## The TheiaProk_Illumina_PE pipeline

TheiaProk is a pipeline for _de novo_ assembly and characterization of bacterial genomes.
It comes in three flavors depending on the input data type: Illumina_PE, Illumina_SE, and ONT.
TheiaProk is accessible from [Dockstore](https://dockstore.org/workflows/github.com/theiagen/public_health_bacterial_genomics/TheiaProk_Illumina_PE:v1.3.0?tab=info), source code is available from
[GitHub](https://github.com/theiagen/public_health_bacterial_genomics/blob/v1.3.0/workflows/wf_theiaprok_illumina_pe.wdl),
and has extensive [documentation](https://theiagen.notion.site/TheiaProk-Workflow-Series-cc66a9dc42a144a789990935465bc9ff). 

In this exercise, the pipeline will run on the [Terra](https://terra.bio/) platform, however the pipeline is known
to work on other cloud platforms as well as via the command line using [miniWDL](https://miniwdl.readthedocs.io/en/latest/getting_started.html).

Briefly, the `assemble_denovo` pipeline consists of the following steps:
1. Input read screening (ensures the quantity of sequence data is sufficient to undertake genomic analysis)
2. Read QC and trimming
3. *de novo* assembly via Shovill
4. Taxonomic assignment via GAMBIT
5. AMR characterization and species-specific typing and tools
6. Various QC outputs


## Terra workspace

For this exercise, we have created a [Terra workspace](https://app.terra.bio/#workspaces/gates-pgs-africacdc/CholGen_Workshop_Feb2024) in advance and loaded in the sequencing read data and workflows. This is a
dataset of Vibrio cholerae sequencing from various African partner institutions.


## Walkthrough

### Clone the workspace

A workspace for these exercises has been created in advance, and contains the required input data organized into distinct tables.
The tables can be explored from the "[**Data**](https://app.terra.bio/#workspaces/gates-pgs-africacdc/CholGen_Workshop_Feb2024/data)" tab.

Before beginning the exercise, the pre-made workspace will be copied to a "clone" that will be yours to use for these exercises. 
Using a cloned workspace will ensure that the compute jobs and their outputs you see are yours alone.

<img width="40%" alt="image" src="https://github.com/broadinstitute/viral-workshops/assets/8513746/7ee1527f-713e-4b27-88b0-bf47cd266278">

 - Navigate to the [workspace for this workshop](https://app.terra.bio/#workspaces/gates-pgs-africacdc/CholGen_Workshop_Feb2024/)
 - Expand the workspace actions menu by clicking the round button with three dots (vertical ellipsis) in the upper right corner
 - Select **Clone**
 - In the modal dialog box that appears: 
  - Give the new (clone) workspace a descriptive `Workspace name` -- for the purposes of this workshop, include your initials or name in order to uniquely identify it (e.g. "CholGen 2024 Joe Smith").
  - Set the `Billing project` to "gates-pgs-training" (if it isn't already).
  - Leave the `Bucket location` and `Description` as their default values.
  - **Do not check** the protected data box or select an Authorization domain -- these will (intentionally) make data access much more difficult and is unnecessary for most work on Terra, especially training workshops.

<img width="40%" alt="image" src="https://github.com/broadinstitute/viral-workshops/assets/8513746/122baba8-432c-482a-b4c5-507fc7f5e6d0">




### Run TheiaProk_Illumina_PE_PHB

Click on the **Workflows** tab on the top. This should lead to a list of analysis workflows that have already been preloaded into your
workspace. One of them is `TheiaProk_Illumina_PE_PHB`.

This will lead to a workflow configuration page where you will need to set parameters and inputs before launching your analysis.
Make sure to set the following:

- The `TheiaProk_Illumina_PE_PHB` "Version:" should be already set to `v1.3.0`, but make sure it is set as such.
- "Run workflow(s) with inputs defined by data table" should be selected (not "file paths").
- "Step 1 — Select root entity type:" should be set to `sample`.
- "Step 2 — **SELECT DATA**" — click on this button and a data selector box will pop up. Check box all six rows of the `sample` table so that we launch multiple assembly jobs at the same time, one for each sample in the table. After selecting the rows, click the **OK** button on the lower right of the pop up box. This should return you to the workflow setup page which should now say that it will run on "6 selected samples" [sic].

<img width="80%" alt="input data selection" src="https://github.com/broadinstitute/viral-workshops/assets/53064/5743430f-46d9-4844-83bd-38ee8b0a480f">

- In the inputs table on the lower part of the page, the following required inputs will need to be set:
  - `assemble_denovo.reads_unmapped_bams` (required) should be set to `this.cleaned_bam`
  - `assemble_denovo.reference_genome_fasta` (required) should be set to `workspace.lasv_reference_scaffold_genomes`
  - `assemble_denovo.trim_clip_db` (required) should be set to `workspace.trim_clip_db`
  - `scaffold.min_unambig` should be set to `0.8`; this corresponds to the fraction of the genome that must be covered by unambiguous bases (i.e. not `N`s) for a successful assembly.
- Click the **SAVE** button after you've set all the inputs.

The resulting workflow launch page should look like this when you are ready:

<img width="80%" alt="image of workflow launch configuration" src="https://github.com/broadinstitute/viral-workshops/assets/53064/51711f0a-00c3-409b-91d9-9ef2ed034a75">

Click the **RUN ANALYSIS** button, which should be dark blue if all required inputs are properly set. 

Another modal dialog box will appear with an input box to enter a (human-readable) text description of the workflows jobs to be launched.
This is a helpful field to describe distinct or distinguishing features of the jobs being submitted, 
so jobs with various parameters or inputs subsets can be quickly located among other jobs that have been run.

<img width="461" max-width="80%" alt="workflow launch description modal" src="https://github.com/broadinstitute/viral-workshops/assets/53064/75070f7e-83ed-463f-b316-0a048f662acc">

Enter a description of your choosing, 
such as "de novo assembly of LASV genomes from six samples, with min_unambig passing threshold set to 0.8"

Click the **LAUNCH** button to start the compute jobs.

This will take you to a job submission status page for your newly launched workflow, 
showing six rows in the bottom table corresponding to the six jobs that have been launched.

<img width="80%" alt="Screenshot 2023-08-14 at 12 44 56" src="https://github.com/broadinstitute/viral-workshops/assets/53064/b6557013-79d2-448b-b99c-2bf4dd698432">

No connectivity or power is required at the client side during this time; the jobs will continue to run on Terra if you navigate away from the page or shutdown your computer.

The total runtime (real-world clock time) should be somewhat independent of whether you launched jobs on 1 or 1,000 samples, as the workflows are executed in parallel on separate cloud compute instances.

Some intermediate outputs are viewable before the full analysis completes, but it's often easier to wait for final results to be loaded into the table.

About 1 day after job completion, total cloud compute costs are displayed on this page (in USD). Prior to that, the run costs are listed as "N/A". 

### Wait for job completion

You will receive an email when each of your submissions complete (along with information about whether they succeeded or failed). 
Additionally, you can also click on the **JOB HISTORY** tab at the top of your workspace to check on the status of your analyses in progress.
When the `TheiaProk_Illumina_PE_PHB` workflow jobs have finished running, you can move on to evaluating the results.
The job submission page for your submission under the Job History tab should look like this when the submissions are complete:

<img width="80%" alt="image of job history" src="https://github.com/broadinstitute/viral-workshops/assets/53064/68eea307-7030-4cd2-8763-127f798e542f">

Depending on some predictable and some unpredictable factors, the `assemble_denovo` jobs should complete within <20 minutes for input data of the sizes provided in this exercise.

## Evaluating results

You can examine the outputs and results of each step of each job via the Job History page, however, for large submissions, 
it is easier to view the saved top level outputs in the data table—in this case, the `sample` table. 

After the `TheiaProk_Illumina_PE_PHB` jobs have completed, the `sample` table should have a number of additional output columns,
including assembly coverage plots for viewing read depth across the genome, `.fasta` sequence files, various intermediate output files, and metrics such as `assembly_length_unambiguous` and `mean_coverage`.

Among the many new columns, the following contain the outputs of the `assemble_denovo` workflow that are worth inspecting first:
 - `final_assembly_fasta` — contains sequence(s) assembled from the input reads (up to one per segment). The sequence(s) may contain ambiguous bases (`N`s) in regions of the genome lacking adequate read depth. All bases present represent coverage by actual reads and not bases imputed from references.
 - `aligned_bam` — contains reads mapped to the sequence of the final assembly ("mapped to self"), in [bam format](https://samtools.github.io/hts-specs/SAMv1.pdf).
 - `coverage_plot` - visualizes read depth as a function of genome location. Peaks indicate regions of high coverage; few reads mapped in regions with values near zero
 - `assembly_length` — the number of bases between the start and end position of the assembled sequence(s); this includes both known bases _and_ **ambiguous** bases (`N`s), and is not representative of the overall success of the assembly process
 - `assembly_length_unambiguous` — the number of distinct positions in the final assemgbly with unambigious bases (i.e. where the bases are known and not `N`). For a complete assembly, `assembly_length_unambiguous` will be close in value to the length of a known reference genome
 - `mean_coverage` — the number of reads mapped to the assembly, divided by the number of unambiguous bases

The metrics for the assemblies should be similar to those in the following table:

| entity:de_novo_assembly_id | assembly_length | assembly_length_unambiguous | mean_coverage      |
|----------------------------|-----------------|-----------------------------|--------------------|
| LASV_NGA_2016_0409         | 9056            | 5292                        | 55.897968197879855 |
| LASV_NGA_2016_0668         |                 |                             |                    |
| LASV_NGA_2016_0759         | 10551           | 10428                       | 161.0294758790636  |
| LASV_NGA_2016_0811         | 8334            | 2717                        | 1.8178545716342693 |
| LASV_NGA_2016_1423         | 10588           | 10588                       | 3051.3962032489612 |
| LASV_NGA_2016_1547         | 9289            | 5363                        | 5.262568629561847  |

Near-complete assemblies were produced for two samples, LASV_NGA_2016_1423 and LASV_NGA_2016_0759. Two produced partial assemblies, LASV_NGA_2016_0409 and LASV_NGA_2016_1547.
The remaining did not yield usable assemblies, though one, LASV_NGA_2016_0811, did have low-depth partial coverage across the genome.

Plots illustrating coverage depth for the assemblies are now available in PDF files listed in the `coverage_plot` column, and included here for reference:
 - [LASV_NGA_2016_0409](https://github.com/broadinstitute/viral-workshops/blob/main/veme-ngs/de_novo_coverage_plots/LASV_NGA_2016_0409.ll2.coverage_plot.pdf)
 - [LASV_NGA_2016_0759](https://github.com/broadinstitute/viral-workshops/blob/main/veme-ngs/de_novo_coverage_plots/LASV_NGA_2016_0759.ll1.cleaned.downsampled.dedup.mapped.coverage_plot.pdf)
 - [LASV_NGA_2016_0811](https://github.com/broadinstitute/viral-workshops/blob/main/veme-ngs/de_novo_coverage_plots/LASV_NGA_2016_0811.ll3.coverage_plot.pdf)
 - [LASV_NGA_2016_1423](https://github.com/broadinstitute/viral-workshops/blob/main/veme-ngs/de_novo_coverage_plots/LASV_NGA_2016_1423.coverage_plot.pdf)
 - [LASV_NGA_2016_1547](https://github.com/broadinstitute/viral-workshops/blob/main/veme-ngs/de_novo_coverage_plots/LASV_NGA_2016_1547.ll4.coverage_plot.pdf)

The two colors shown each plot correspond to the two segments of the LASV genome (L, and S, respectively).

<img width="80%" alt="assembly coverage plot overview" src="https://github.com/broadinstitute/viral-workshops/assets/53064/2c10640a-7226-4f80-a769-e796d261361e" usemap="#image-map">

### Inspecting results from this walkthrough

Recall that when the `assemble_denovo` workflow was configured, the `min_unambig` parameter was set to `0.8`. 
That value limits successful assemblies to those with ≥80% of bases known (i.e. not `N`).
Lowering the `min_unambig` threshold will make the assembly process more permissive in the assemblies deemed "successful."

Try lowering `min_unambig` based on the "failing" jobs observed during the first set of assembly jobs, and compare the resulting assemblies.


The columns shown or hidden for a data table can be configured by clicking the<img width="26" display="inline" style="top:0.4em;position:relative;" alt="column settings gear icon" src="https://github.com/broadinstitute/viral-workshops/assets/53064/d5123eb3-ccd3-4a9d-8a2a-4ef62ae9bd65">**SETTINGS** button above the table and selecting columns as desired.

<img width="761" alt="column settings button" src="https://github.com/broadinstitute/viral-workshops/assets/53064/6bbb1bc7-17e5-447e-bd12-184a67f16504">

<img width="822" alt="displayed column selector" src="https://github.com/broadinstitute/viral-workshops/assets/53064/355f570f-a9bf-4290-b09d-bbbb91ccbef2">

