# Reference-based assembly for O1 Vibro cholerae

This is a walkthrough of a workflow to perform reference-based assembly of Vibrio cholerae.

# Contents
{:.no_toc}

1. Table of Contents
   {:toc}

## Description of workflows
The exercise will utilize workflows based on the bacpage bioinformatics pipeline.
The workflow itself is available on [Dockstore](https://dockstore.org/my-workflows/github.com/CholGen/bacpage/bacpage-assemble).
The source code for this workflow as well as bacpage is available on [GitHub](https://github.com/CholGen/bacpage/blob/master/bacpage/workflows/assemble.wdl)
This workflow is designed for execution on the [Terra](https://terra.bio) platform, but can also be run on other cloud 
platforms as well as on the command line using miniWDL.

Briefly, the workflow generates a reference-based assembly from raw Illumina paired-end sequencing data. 
The process comprises five main steps:
1. Align paired-end FASTQ files to a reference genome, generating BAM files.
2. Call variants in the BAM file relative to the reference.
3. Generate a consensus sequence from the variants.

<img width="40%" alt="image" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/486245/6b662628-6d4c-442a-b1fa-0a9e32bff061">

## Terra workspace setup
This tutorial assumes your Terra workspace has been setup according to [Workspace setup](workshop/workspace-setup.md)
and [Uploading your own data](workshop/uploading-data.md).
Specifically, this tutorial expects your workspace to contain a table with at least the following columns.

| entity:sample_id | read1                            | read2                            |
|------------------|----------------------------------|----------------------------------|
| sampleA          | gs://path/to/sampleA_R1.fastq.gz | gs://path/to/sampleA_R2.fastq.gz |

## Analysis walkthrough
From your workspace, click on the Workflows tab on the top. This should lead to a list of analysis workflows that have 
already been preloaded into your
workspace. One of them is `bacpage-assemble`. Click on `bacpage-assemble`.
This will lead to a workflow launch page where you will need to set the parameters and inputs before launching your analyses.
Make sure to set the following:
 - The `bacpage-assemble` "Version:" should be already set to `master`, but make sure it is set as such.
 - "Run workflow(s) with inputs defined by data table" should be selected (not "file paths").
 - "Step 1 -- Select root entity type:" should be set to `sample`.
 - "Step 2 -- SELECT DATA" -- click on this button and a data selector box will pop up. Check the box on all 
   rows of the `sample` table so that we launch a job for each sample in the table. Hit the OK button 
   on the lower right of the pop up box. This should return you to the workflow setup page which should now say that 
   it will run on "X selected samples" (where X is the number of samples in your table).
   - Alternatively, you might only run the workflow on the samples which where classified as _Vibrio cholerae_ by 
     the TheiaProk workflow. To do this, sort the `sample` table by the `gambit_predicted_taxon` column and select 
     only the samples with `Vibrio cholerae` listed. 
 - In the inputs table, we will need to populate the following required inputs:
   - `reference_based_assembly.read1` (required) should be set to `this.read1`
   - `reference_based_assembly.read2` (required) should be set to `this.read2`
   - `reference_based_assembly.sample_name` (required) should be set to `this.sample_id`
 - Click the SAVE button after you've set all the inputs. 

The resulting workflow launch page should look like this when you are ready:

<img width="80%" alt="image" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/486245/5019dd7c-348a-49a2-b3f6-4f0ed7f5de94">

Click "RUN ANALYSIS" (which should be dark blue if you've filled in all inputs properly). This will take you to a job submission
status page for your newly launched workflow, showing rows in the bottom table corresponding to the samples that you 
are analyzing.

## Wait for job completion
You will receive an email when each of your submissions complete (along with information about whether they succeeded or failed).
Additionally, you can also click on the JOB HISTORY tab at the top of your workspace to check on the status of your analyses in progress.
When both `bacpage-assemble` is complete, you can move on to evaluating the results. 

## Evaluating results
You can examine the outputs and results of each step of each job via the Job History page, however, for large 
submissions, it is easier to view the saved top level outputs in the data table--in this case, the `sample` table. 
The `sample` table now has a number of additional output columns, including consensus genomes (FASTA), various 
quality assessment reports, and various numeric counts and metrics. 

#### Metrics
We will evaluate these in more depth in the [_Vibrio cholerae_ QC reports](workshop/report-bacpage-qcviz) tutorial next, 
but the key outputs of interest are:
 - The total number of reads found for a sample: `total_reads`
 - The number of reads that were successfully mapped against the O1 Vibrio cholerae reference genome: `mapped_reads`
 - The percent of reads that successfully mapped: `percent_mapped_reads`
 - The percent of the reference genome that is covered by at least 10 reads (by default): `percent_coverage`
 - The median depth at every position of the reference genome: `median_depth`

For instance, you might use these metrics to select the samples you will included in a phylogenetic analysis. 
We recommend only selecting samples that have greater than 90% mapped reads, 90% coverage of the reference genome, 
and 10X median depth.

## Other related resources
 - For the command line version of this same workflow see the [bacpage documentation](https://cholgen.github.io/sequencing-resources/reference-based-assembly.html#running-the-assembly-pipeline).
 - 
