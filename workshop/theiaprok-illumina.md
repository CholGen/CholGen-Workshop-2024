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
to work on other cloud platforms as well as via the command line using [miniWDL](https://miniwdl.readthedocs.io/en/latest/getting_started.html). We will use the Illumina_PE flavor of this pipeline, as the data used in this workshop is exclusively paired end Illumina data.

Briefly, the `TheiaProk_Illumina_PE_PHB` pipeline consists of the following steps:
1. Input read screening (ensures the quantity of sequence data is sufficient to undertake genomic analysis)
2. Read QC and trimming
3. *de novo* assembly via Shovill
4. Taxonomic assignment via GAMBIT
5. AMR characterization and species-specific typing and tools
6. Various QC outputs

![TheiaProk_Illumina_PEv1 3 0_Vibrio](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/5261bcc6-fa32-4907-b09e-be90d74f56ab)

## Terra workspace

For this exercise, we have created a [Terra workspace](https://app.terra.bio/#workspaces/gates-pgs-africacdc/CholGen_Workshop_Feb2024) in advance and loaded in the sequencing read data and workflows. This is a dataset of _Vibrio cholerae_ sequencing from various African partner institutions.

In a previous session, you should have already cloned this template workspace for the workshop into your own working copy for analyses ([walkthrough](workspace-setup-cholgen.md))

## Walkthrough

Navigate to the cloned workspace you created earlier in the workshop.

### Run TheiaProk_Illumina_PE_PHB

Click on the **Workflows** tab on the top. This should lead to a list of analysis workflows that have already been preloaded into your workspace. One of them is `TheiaProk_Illumina_PE_PHB`.

This will lead to a workflow configuration page where you will need to set parameters and inputs before launching your analysis. Make sure to set the following:

- The `TheiaProk_Illumina_PE_PHB` "Version:" should be already set to `v1.3.0`, but make sure it is set as such.
- "Run workflow(s) with inputs defined by data table" should be selected (not "file paths").
- "Step 1 — Select root entity type:" should be set to `cholera_sample`.
- "Step 2 — **SELECT DATA**" — click on this button and a data selector box will pop up. Check box all 221 rows of the `cholera_sample` table so that we launch multiple assembly jobs at the same time, one for each sample in the table. In order to check all 221 rows, you will need to click the downward pointing black triangle next to the empty checkbox on the header row of the table. This will pull down a menu and you can select "All (221)" from the menu. This will return you to the sample selector table with all 221 rows filled in. After selecting the rows, click the **OK** button on the lower right of the pop up box. This should return you to the workflow setup page which should now say that it will run on "221 selected cholera_samples".

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/797820b2-1062-4d64-b662-752c8b09a5fc)

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/fd842e29-441b-4dd7-aa6c-3ca2d4ee3cd0)


- In the inputs table on the lower part of the page, the following required inputs will need to be set:
  - `assemble_denovo.reads_unmapped_bams` (required) should be set to `this.cleaned_bam`
  - `assemble_denovo.reference_genome_fasta` (required) should be set to `workspace.lasv_reference_scaffold_genomes`
  - `assemble_denovo.trim_clip_db` (required) should be set to `workspace.trim_clip_db`
  - `scaffold.min_unambig` should be set to `0.8`; this corresponds to the fraction of the genome that must be covered by unambiguous bases (i.e. not `N`s) for a successful assembly.
- Click the **SAVE** button after you've set all the inputs.

The resulting workflow launch page should look like this when you are ready:

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/255e2cca-f71a-4de9-b8b1-3a6df40ec951)

TO DO -- optional parameters:
![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/ae5cbc11-a742-4706-bb9e-69e4f1c75a2e)

Click the **RUN ANALYSIS** button, which should be dark blue if all required inputs are properly set. 

Another modal dialog box will appear with an input box to enter a (human-readable) text description of the workflows jobs to be launched.
This is a helpful field to describe distinct or distinguishing features of the jobs being submitted, 
so jobs with various parameters or inputs subsets can be quickly located among other jobs that have been run.

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/a7440035-a721-4aac-bf9f-11b5792df647)

Enter a description of your choosing, 
such as "de novo assembly of LASV genomes from six samples, with min_unambig passing threshold set to 0.8"

Click the **LAUNCH** button to start the compute jobs.

This will take you to a job submission status page for your newly launched workflow, 
showing six rows in the bottom table corresponding to the six jobs that have been launched.

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/dd758c70-0dac-46e9-8d05-dd354f7fe298)

No connectivity or power is required at the client side during this time; the jobs will continue to run on Terra if you navigate away from the page or shutdown your computer.

The total runtime (real-world clock time) should be somewhat independent of whether you launched jobs on 1 or 1,000 samples, as the workflows are executed in parallel on separate cloud compute instances.

Some intermediate outputs are viewable before the full analysis completes, but it's often easier to wait for final results to be loaded into the table.

About 1 day after job completion, total cloud compute costs are displayed on this page (in USD). Prior to that, the run costs are listed as "N/A". 

### Wait for job completion

You will receive an email when each of your submissions complete (along with information about whether they succeeded or failed). 
Additionally, you can also click on the **JOB HISTORY** tab at the top of your workspace to check on the status of your analyses in progress.
When the `TheiaProk_Illumina_PE_PHB` workflow jobs have finished running, you can move on to evaluating the results.
The job submission page for your submission under the Job History tab should look like this when the submissions are complete:

<img width="1149" alt="image" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/ea0bdc91-f1de-442f-ae23-3cd3750b1ab1">

Depending on some predictable and some unpredictable factors, the `TheiaProk_Illumina_PE_PHB` jobs should complete within an hour for input data of the sizes provided in this exercise.

## Evaluating results

You can examine the outputs and results of each step of each job via the Job History page, however, for large submissions, 
it is easier to view the saved top level outputs in the data table—in this case, the `sample` table. 

After the `TheiaProk_Illumina_PE_PHB` jobs have completed, the `cholera_sample` table should have a number of additional output columns,
including assembly coverage plots for viewing read depth across the genome, `.fasta` sequence files, various intermediate output files, and metrics such as `assembly_length_unambiguous` and `mean_coverage`.

Among the many new columns, the following contain the outputs of `TheiaProk_Illumina_PE_PHB` that are worth inspecting first:
 - `gambit_predicted_taxon` - bacterial species prediction with the GAMBIT tool
 - `ani_top_species_match`, `ani_output_tsv` - bacterial species prediction with the ANI tool
 - `kmerfinder_top_hit` - bacterial species prediction via the kmerfinder tool
 - `serotypefinder_serotype` - serotyping via the serotypefinder tool (generic for most bacteria)
 - `srst2_vibrio_serogroup`, `srst2_vibrio_detailed_tsv` - serotyping via the srst2 tool (vibrio-specific)
 - `amr_finderplus_virulence_genes` - AMR calling via AMR Finder Plus
 - `resfinder_predicted_pheno_resistance` - AMR calling ia ResFinder
 - `plasmid_finder_plasmids` - plasmid identification
 - `clean_read_screen` - read screening results
 - `num_reads_clean_pairs` - number of cleaned reads
 - `number_contigs` - number of de novo contigs produced
 - `prokka_gbf`, `prokka_gff`, `prokka_sqn` - Genbank submission files from the Prokka tool

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

### Inspecting results from this walkthrough

Recall that when the `assemble_denovo` workflow was configured, the `min_unambig` parameter was set to `0.8`. 
That value limits successful assemblies to those with ≥80% of bases known (i.e. not `N`).
Lowering the `min_unambig` threshold will make the assembly process more permissive in the assemblies deemed "successful."

Try lowering `min_unambig` based on the "failing" jobs observed during the first set of assembly jobs, and compare the resulting assemblies.


The columns shown or hidden for a data table can be configured by clicking the<img width="26" display="inline" style="top:0.4em;position:relative;" alt="column settings gear icon" src="https://github.com/broadinstitute/viral-workshops/assets/53064/d5123eb3-ccd3-4a9d-8a2a-4ef62ae9bd65">**SETTINGS** button above the table and selecting columns as desired.

<img width="761" alt="column settings button" src="https://github.com/broadinstitute/viral-workshops/assets/53064/6bbb1bc7-17e5-447e-bd12-184a67f16504">

<img width="779" alt="image" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/ed9c9f55-ba05-4bb1-8860-280f118b4f3c">
