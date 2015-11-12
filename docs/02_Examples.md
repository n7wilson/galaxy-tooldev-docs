
We've provided some example Galaxy tools to give you an idea of how to develop tools for the Challenge. Feel free to download these tools and play around with them to get a better sense of how to use Galaxy, Docker and the rest of the Challenge resources.

## Download the Example Tools
The example code can be found on GitHub at https://github.com/Sage-Bionetworks/SMC-Het-Challenge-Examples. In order to view the tools in your instance of Galaxy you must download them in the /opt/galaxy/tools folder of your VM instance.

To do this execute the following commands in you VM:
```
$ cd /opt/galaxy/tools/
$ git clone https://github.com/Sage-Bionetworks/SMC-Het-Challenge-Examples.git
$ cd SMC-Het-Challenge-Examples
```

After you have downloaded the tools you will need to restart Galaxy so that the example tools show up by executing the command `restart_galaxy` in your VM instance.

Now that Galaxy is setup you can access Galaxy instance through your [Google Developers Console](https://console.developers.google.com/) by going to Compute -> Compute Engine -> VM instances and clikcing on the 'External IP' column associated with your VM instance.

Once you are in Galaxy you can find the example tools under 'Imported Tools' in the panel on the lefthand side.

> If the example tools do not display in the tool selection panel on the left hand side of the window, try running `restart_galaxy` to restart the Galaxy server

## Importing Tumour Data
In order to use any of the examples you will first need to import the training data to your history in Galaxy. This will allow you to use the training data as input to the tools. To import the training data for Tumour1:
* Go to Data Libraries under the Shared Data tab at the top of your Galaxy page
* Select 'Imported Data'
* Check the box next to Tumour1 and click 'Go'
${image?fileName=Galaxy_import_data.png}
* All the data will now appear in your history
${image?fileName=Galaxy_tumour1_imported.png}
_Note: to import other tumour sets follow the same steps as above but replace 'Tumour1' with the desired tumour_

> You should only use the 'mutect.vcf' and 'battenberg.txt' files for input into the example tools. If you use any of the files with 'truth' in the name the tools will not work properly.

## Using the DPC Example

INPUT:
VCF File

OUTPUT:
Submission files for subchallenges 1A, 1B, 1C, 2A and 2B

WORKFLOW FILE:
${previewattachment?fileName=Galaxy-Workflow-DPC_Workflow.ga}

The DPC tool is the simpler of the two examples, since it only uses one Galaxy tool and does not require you to build the Docker image for the tools yourself, so it is advisable to start with this one.

To use the DPC tool head to your Galaxy instance and import the Training data:
1. Select the DPC tool in the left hand tool panel under Imported Tools
2. Find the 'mutect.vcf' VCF file in the 'VCF file' selection in the center panel
3. Click the `Execute` button at the bottom of the form

Once the tool has finished running the output data for the evaluator will be found in your history.

> If you get the following error: `/var/run/docker.sock: no such file or directory.` `Are you trying to connect to a TLS-enabled daemon without TLS?`, try restarting the docker daemon by running the following command in your VM instance: `sudo service docker restart`.

## Using the PhyloWGS Example

INPUT:
VCF File
CNA File

OUTPUT:
Submission files for subchallenges 1A, 1B, 1C, 2A, 2B, 3A and 3B

WORKFLOW FILE:
${previewattachment?fileName=Galaxy-Workflow-PhyloWGS.ga}

The PhyloWGS example is slightly more complicated than the DPC example since it requires some extra setup and also requires you to use multiple tools within Galaxy. However, PhyloWGS provides submission files for all subchallenges, not just subchallenges 1 and 2.

### Setup: Building the Docker Container

Before you run the PhyloWGS tool in Galaxy you will need to build the PhyloWGS Docker container on your VM so that you have all the dependencies necessary for the PhyloWGS tool to run.
To setup the Docker Environment:
* Go to the PhyloWGS directory in the SMC-Het-Challenge-Examples repository by running the following command:
`$ cd /opt/galaxy/tools/SMC-Het-Challenge-Examples/PhyloWGS`
* Build the Docker container by running the following command:
`$ planemo docker_build .`
* Restart your Galaxy instance to see the changes by running the following command:
`$ restart_galaxy`

### Tool 1 - PhyloWGS CNVs Prep: Extracting CNAs

The first step to running the PhyloWGS code is to extract the CNAs from the battenberg file:
1. Select the 'PhyloWGS CNVs Prep' tool in the left hand tool panel
2. Find the 'battenberg.txt' file from the Tumour that you imported in the 'battenberg' selection in the center panel
3. Click the `Execute` button at the bottom of the form
_Note: you can also modify the cellularity to get more accurate results if you wish_

The once the tool has run the CNVs data will be found in your history.

### Tool 2 - PhyloWGS Tool Input Prep: Extracting SSMs

Next you will need to extract the SSMs from the mutect file and format the CNVs for input into PhyloWGS:
1. Select the 'PhyloWGS Tool Input Prep' tool in the left hand tool panel
2. Find the CNVs output data from Tool 1 in the 'cnvs' selection in the center panel
3. Find the 'mutect.vcf' VCF file from your imported Tumour in the 'vcf_file' selection in the center panel
4. Click the `Execute` button at the bottom of the form
_Note: you can subsample the mutations (meaning the algorithm only selects the given number of mutations instead of all those identified) to improve the algorithm speed, however you should NOT subsample fewer mutations than are found in the mutect file as this will cause errors when evaluating the algorithm._

### Tool 3 - PhyloWGS Tool: Run the Algorithm

Now that the input is setup we can run the algorithm
1. Select the 'PhyloWGS Tool' tool in the left hand tool panel
2. Find the Mutation output data from Tool 2 in the 'Mutation Data' selection in the center panel
3. Find the CNV output data from Tool 2 in the 'CNV Data' selection in the center panel
4. Click the `Execute` button at the bottom of the form
_Note: This algorithm takes a very long time (up to 1 day using the default settings). You can reduce the number of MCMC samples and the number of Metropolis-Hastings iterations to speed up the algorithm._

### Tool 4 - PhyloWGS Report Format: Format the Output for Report Writing

Once the algorithm has finished there are two formatting steps to prepare the output data in the correct format for submission:
1. Select the 'PhyloWGS Report Format' tool in the left hand tool panel
2. Enter the ID of the Tumour sample used in running the PhyloWGS algorithm
3. Find the trees file from Tool 3 in the 'Trees' selection in the center panel
4. Click the `Execute` button at the bottom of the form

### Tool 5 - PhyloWGS Submission Format: Format the Output for Challenge Submission:

Now the final step is to extract submission data for each subchallenge from the Report Writing data:
1. Select the 'PhyloWGS Submission Format' tool in the left hand tool panel
3. Find the trees.json.gz file from Tool 3 in the 'trees' selection in the center panel
3. Find the mutations.json.gz file from Tool 3 in the 'mutations' selection in the center panel
3. Find the mutations_assignments.json.gz file from Tool 3 in the 'mutation assignments' selection in the center panel
4. Click the `Execute` button at the bottom of the form

Once the tool has finished running the output data for the evaluator will be found in your history.

> If you get the following error: `/var/run/docker.sock: no such file or directory.` `Are you trying to connect to a TLS-enabled daemon without TLS?`, try restarting the docker daemon by running the following command in your VM instance: `sudo service docker restart`.

## Evaluating the Example Tools
After you have finished running one of the example tools you can run the results through the Evaluator Tool to see how these tools performed.

The process for running the Evaluator tool is similar to running any of the other tools:
1. Select the 'SMC-Het Evaluator' tool in the left hand tool panel under 'Evaluation Tools'
2. Select the Tumour you imported to run the example tool in the 'Sample' selection
3. Select 'Yes' for any of the subchallenges that you wish to submit to
_Note: the DPC tool provides output for subchallenges 1 and 2 only while the PhyloWGS tool provides output for all three subchallenges._
4. For each of the subchallenges that you are submitting to select the output file with the corresponding subchallenge name in input file section
5. Click the `Execute` button

The results of your sample tool's evaluation (in dictionary format) will then appear in your history.

> Keep the scores of these tools in mind when developing your own tool try to beat them!

## Example Workflows
Workflow files for both sample tools above can be downloaded here:
DPC - ${previewattachment?fileName=Galaxy-Workflow-DPC_Workflow.ga}
PhyloWGS - ${previewattachment?fileName=Galaxy-Workflow-PhyloWGS.ga}

Once uploaded to your Galaxy instance these files will allow you to run the entire tool and evaluate the results with one click.

To use these workflows in your Galaxy instance:
1. Go to the Workflow tab in your Galaxy instance
2. Click the 'Upload or import workflow' button in the top righthand corner
3. Click 'Browse' and find the file containing the workflow you want to upload
4. Click 'Import'
5. Click on the newly imported workflow and then click 'Run'
6. Select the input for you workflow (you will need to import the tumour data to your history)
7. Click 'Run workflow'