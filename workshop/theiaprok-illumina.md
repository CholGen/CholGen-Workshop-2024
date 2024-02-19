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

It generally assumes that you have performed DNA whole genome shotgun sequencing of bacterial isolates and attempts to assemble, identify, and characterize/type the dominant bacteial species present in the data. Some of the typing tools are species specific and are invoked only if those species are detected. For this workshop, the _Vibrio cholerae_ specific typing tool is called _srst2_ and will only be called if cholera was found in the data.

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
  - `theiaprok_illumina_pe.read1_raw` (required) should be set to `this.read1`
  - `theiaprok_illumina_pe.read2_raw` (required) should be set to `this.read2`
  - `theiaprok_illumina_pe.samplename` (required) should be set to `this.cholera_sample_id`
The following optional inputs are good to set to enable various species and strain typing tools:
  - `theiaprok_illumina_pe.call_ani` should be set to `true` to enable ANI-based species identification.
  - `theiaprok_illumina_pe.call_kmerfinder` should be set to `true` to enable kmerfinder-based species identification.
  - `theiaprok_illumina_pe.call_resfinder` should be set to `true` to enable resfinder-based AMR calling.
- Click the **SAVE** button after you've set all the inputs.

The top resulting workflow launch page should look like this when you are ready:

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/255e2cca-f71a-4de9-b8b1-3a6df40ec951)

If you scroll farther down the workflow parameters on the launch page, you should see these optional parameters filled in:
![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/ae5cbc11-a742-4706-bb9e-69e4f1c75a2e)

Click the **RUN ANALYSIS** button, which should be dark blue if all required inputs are properly set. 

Another modal dialog box will appear with an input box to enter a (human-readable) text description of the workflows jobs to be launched.
This is a helpful field to describe distinct or distinguishing features of the jobs being submitted, 
so jobs with various parameters or inputs subsets can be quickly located among other jobs that have been run. This is simply a note to your future self about what you did and can be changed later on.

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/a7440035-a721-4aac-bf9f-11b5792df647)

Enter a description of your choosing, 
such as "de novo assembly and characterization of 221 bacterial samples"

Click the **LAUNCH** button to start the compute jobs.

This will take you to a job submission status page for your newly launched workflow, 
showing six rows in the bottom table corresponding to the six jobs that have been launched.

![image](https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/dd758c70-0dac-46e9-8d05-dd354f7fe298)

No connectivity or power is required at the client side after this point; the jobs will continue to run on Terra if you navigate away from the page or shutdown your computer.

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
it is easier to view the saved top level outputs in the data table—in this case, the `cholera_sample` table. 

After the `TheiaProk_Illumina_PE_PHB` jobs have completed, the `cholera_sample` table should have a a large number of additional output columns,
including data files and reports, assembly metrics, and typing results.

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

OPTIONAL: if the number of columns is too much to look at, you can create saved "views" of your data tables that focus on a smaller number of columns of interest (such as the ones above). The columns shown or hidden for a data table can be configured by clicking the<img width="26" display="inline" style="top:0.4em;position:relative;" alt="column settings gear icon" src="https://github.com/broadinstitute/viral-workshops/assets/53064/d5123eb3-ccd3-4a9d-8a2a-4ef62ae9bd65">**SETTINGS** button above the table and selecting columns as desired.

<img width="761" alt="column settings button" src="https://github.com/broadinstitute/viral-workshops/assets/53064/6bbb1bc7-17e5-447e-bd12-184a67f16504">

<img width="779" alt="image" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/8513746/ed9c9f55-ba05-4bb1-8860-280f118b4f3c">

OPTIONAL: the Search box in the upper right of the data table view allows you to focus on certain rows that contain search patterns of interest. For example, enter `MOZ-` in the Search box to focus only on data originating from Mozambique. Or `O1` to focus on samples that were typed as _Vibrio cholerae_ O1.

### Inspecting results from this walkthrough

**TO DO: replace this section**
Recall that when the `assemble_denovo` workflow was configured, the `min_unambig` parameter was set to `0.8`. 
That value limits successful assemblies to those with ≥80% of bases known (i.e. not `N`).
Lowering the `min_unambig` threshold will make the assembly process more permissive in the assemblies deemed "successful."

Try lowering `min_unambig` based on the "failing" jobs observed during the first set of assembly jobs, and compare the resulting assemblies.


