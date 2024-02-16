# Uploading data to your Terra workspace

This is a walkthrough demonstrating how to upload data to your Terra workspace. You will upload both sequencing files and tabular data to the workspace.

# Contents
{:.no_toc}

1. Table of Contents
{:toc}




##Why use the Data Uploader?

The data Uploader automates and simplifies the process of uploading data and a corresponding data table to a workspace. Although you can upload files directly in a workspace, usually you need to add all of the URLs (the "gs://bucket-id/filename" file paths) that point to uploaded files in the table manually or with scripting. The Data Uploader does this for you, so you don't have to worry about programmatically generating the list of bucket URLs.

##How to use the Data Uploader

You can access the Data Uploader directly, or from within a workspace.

To access directly
If you don't want to start from a workspace, go to https://app.terra.bio/#upload. 

To access from within a workspace
Click the Import Data button at the top of the Data page and select Open data uploader.






Note: The TSV you upload to generate a table for your data will need to include the file name, just not the full path. In other words, the Data Uploader will replace your-data-file.vcf with gs:fc-3cf82dc8-62a0-4518-ac35-89e03debe3d7/your-data-file.vcf.