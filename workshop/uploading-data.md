# Uploading data to your Terra workspace

This is a walkthrough demonstrating how to upload data to your Terra workspace. You will upload both sequencing files and tabular data to the workspace.

# Contents
{:.no_toc}

1. Table of Contents
{:toc}




## Why use the Data Uploader?

The data Uploader automates and simplifies the process of uploading data and a corresponding data table to a workspace. Although you can upload files directly in a workspace, usually you need to add all of the URLs (the "gs://bucket-id/filename" file paths) that point to uploaded files in the table manually or with scripting. The Data Uploader does this for you, so you don't have to worry about programmatically generating the list of bucket URLs.

## How to use the Data Uploader

<img width="1275" alt="Data-uploader_Steps-to-use_diagram" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/7e955059-d1c5-4e46-ae38-45b809dc6031">

### Step 1: Access data uploader from your workspace
You can access the Data Uploader directly, or from within a workspace.

To access directly
If you don't want to start from a workspace, go to https://app.terra.bio/#upload. 

To access from within a workspace
Click the Import Data button at the top of the Data page and select Open data uploader.

![Screenshot 2024-02-16 at 2 57 04 PM](https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/2657f57d-9dfb-4a97-b3b1-a82e733d249e)

Next, select the workspace that you cloned previously in the workshop.

![Screenshot 2024-02-16 at 5 00 24 PM](https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/155f87e9-0abc-4f47-b17c-99c8baadb0c0)

### Step 2: Create/choose a data collection
Once you've selected the target workspace, you'll be prompted to either create a "collection" or select an existing one. Name your collection and click `Create collection`

![Screenshot 2024-02-16 at 5 02 09 PM](https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/f0b586f0-879b-469e-9af0-d2eb045366fc)


*What is a data collection?*
Collections are a way to organize your data files into groups; like files in local storage. You can use distinct collections if you add data for different organisms, different experimental methodologies, or different sequencing technologies to the same workspace. Each collection will have its own associated data table with metadata like the file ID, the URL of the data file in workspace storage, and any other useful details. 

### Step 3: Upload files

Once you have a collection selected, if you scroll down, you'll see an area prompting you to upload your files. Upload your files, either by dragging-and-dropping them on the page, or clicking the blue plus button at the bottom right to browse the files from your computer:

<img width="1340" alt="Data-uploader_Create-a-new-collection_Screen shot" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/25ae9a21-a4f6-45d1-a749-13b4e3ce4ee7">

### Step 4: Upload you data table (TSV)

The final step is to create a table in the workspace that lists the data and any associated metadata. You'll upload a TSV file that includes, at minimum, a unique ID for each file and the file names of the data you just uploaded.

 *Note*: The TSV you upload to generate a table for your data will need to include the file name, just not the full path. In other words, the Data Uploader will replace your-data-file.vcf with gs:fc-3cf82dc8-62a0-4518-ac35-89e03debe3d7/your-data-file.vcf.

After clicking `NEXT >` in the data file upload step, you'll see a prompt to upload your metadata TSV. You'll have the option to either drag-and-drop, or click the blue upload button to select files from your local machine. Note: Data Uploader will only accept .TSV or .TXT files.

<img width="1304" alt="Data-uploader_Drag-and-drop-TSV-files_Screen shot" src="https://github.com/CholGen/CholGen-Workshop-2024/assets/63110916/a0ab0b3a-82c6-409c-b31b-26d2b91d009e">

To complete this process, once the TSV file upload is complete, click Create table.

### What to expect in the target workspace Data page
The data files will be in workspace storage - in a directory called uploads in a folder named however you named the collection. You can see these files by navigating to the Files icon under Other data (left-hand side).

The metadata will be in a data table. 

To view a video tutorial of how to use the data uploader, see (here) (https://youtu.be/DTU4rON0ycI?si=cW0XXKW5yj8fPxH1)



