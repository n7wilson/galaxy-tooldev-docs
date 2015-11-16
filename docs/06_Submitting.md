
Submitting a workflow to the SMC-Het Challenge
==============================================

${toc}

A submission to the challenge is a [Galaxy workflow](https://wiki.galaxyproject.org/Learn/AdvancedWorkflow). Submitting the workflow is done by the `dream_galaxy_submit` script found in the GitHub repo [SMC-Het-Challenge](https://github.com/Sage-Bionetworks/SMC-Het-Challenge). This script will scan a Galaxy workflow, download all of the relevant tools and then upload them to your Synapse Project.
_Note: using the Evaluator tool does NOT submit your algorithm_

> Your algorithm will not be scored if it uses more than 7$ of compute time in Google Compute Engine or takes longer than 200 hours to run for any of the 50 test tumours. You should test your algorithm on the training tumours in order to ensure your algorithm does not go over the given computational limits. For more information on Google Compute Engine pricing click here: [https://cloud.google.com/compute/pricing](https://cloud.google.com/compute/pricing).

In Galaxy, workflows are created in one of two ways. The first way is to extract a workflow from the 'History', then edit it to create a streamlined workflow. The second is to create the workflow from scratch in the [workflow editor](https://wiki.galaxyproject.org/Learn/AdvancedWorkflow/BasicEditing/WorkflowEditorUnannotated).

##Extracting a Workflow From Your History
Galaxy lets you perform data analysis operations, keeping a history of the tools you run and their parameters. To extract a workflow from your history:
1. Select the 'History Options' drop down menu on the right hand of the Analysis panel above the history, which looks like a gear.
2. Click on 'Extract Workflow', which will give you the option of creating a workflow based on your previous actions.
3. Clean up the workflow in the Galaxy Editor so that it follows the requirements for submitting to the Challenge

##Creating a Workflow From Scratch
If you don't want to use your history to create a workflow you can also build one from scratch. To do this:
1. Select the Workflow menu at the top of the page
${image?fileName=Galaxy_workflow_menu.png}
2. Once in the Workflow section, select 'Create new Workflow'
3. Name your workflow and hit the 'Create' button.
${image?fileName=Galaxy_workflow_dpc.png}
You will then be brought to the workflow editor.
${image?fileName=Galaxy_workflow_editor.png}
4. Once in the workflow editor, find the tools you want to include in the panel on the lefthand side and click them to create add them to your workflow in the central panel (including the evaluator)
${image?fileName=Galaxy_workflow_editor_tool.png}
5. Create inputs to the workflow by selecting Inputs -> Input dataset at the bottom of the panel on the lefthand side
${image?fileName=Galaxy_workflow_editor_input.png}
6. Edit the tool/dataset names and parameters in the panel on the righthand side
_Note: you will need to modify the attributes of the evaluator to accept input for whatever subchallenges you are submitting to._
${image?fileName=Galaxy_workflow_editor_attributes.png}
7. Connect the tools and input data in the order you want to execute them by dragging 'connection noodles' from the outputs on the right hand side of one tool/dataset to the inputs on the left hand side of the next tool.
Click the '*' next to the evaluator output to indicate that this is the final step in the workflow.
${image?fileName=Galaxy_workflow_editor_connections.png}
8. Save your workflow by clicking Settings (the gear button in the top righthand corner of the central pane) -> Save

## Sample Workflows
Sample workflows for the two sample tools provided in SMC-het-Challenge-Examples can be downloaded here:
DPC - ${previewattachment?fileName=Galaxy-Workflow-DPC_Workflow.ga}
PhyloWGS - ${previewattachment?fileName=Galaxy-Workflow-PhyloWGS.ga}

To use these workflows in your Galaxy instance:
1. Go to the Workflow tab in your Galaxy instance
2. Click the 'Upload or import workflow' button in the top righthand corner
3. Click 'Browse' and find the file containing the workflow you want to upload
4. Click 'Import'
5. Click on the newly imported workflow and then click 'Run'
6. Select the input for you workflow (you will need to import the tumour data to your history)
7. Click 'Run workflow'

##Workflow Requirements
1. Inputs must be labeled by the expected file type. The VCF input file is labeled 'VCF\_INPUT' and the copy number input is labeled 'CNA\_INPUT'
2. There must be no tools with parameters defined to be 'Set At Runtime'
3. There must be one and only one instance of the 'SMC-Het Evaluator' tool [found on GitHub](https://github.com/Sage-Bionetworks/SMC-Het-Challenge) as part of the workflow.

##Annotating the Workflow

Once your workflow has been created you will need to do some minor edits to make sure the leaderboard system can correctly assign inputs to the workflow and collect the right file for evaluation. In the workflow editor, you will click the different elements of the workflow to bring up their info in the 'Details' panel on the right hand side of the screen. When requested to add annotations to a element in the workflow, look for the `Edit Step Attributes` subsection in the tool and use the `Annotation / Notes:` section to add annotation text to the step.

To create a workflow that can be used by the evaluation system you need to:
1. Use the 'Annotation' field to name one of the dataset inputs `VCF_INPUT`
2. For reference files, upload the required file to your project in Synapse, then put the Synapse ID (ie syn12345) into the Annotation fields of the workflow data inputs
3. For the output file, use the 'Post Job Rename' option in the tool option menu to rename the submission file to 'OUTPUT'

${image?fileName=create_workflow.gif}

##Test the workflow

Once you have created a workflow you can test it in Galaxy by going to the Analyze Data tab, selecting Workflows -> All workflows in the lefthand panel and finding your workflow.
{image?fileName=Galaxy_workflow_run.png}
You can then execute your workflow by clicking your workflow, passing in the necessary inputs and then clicking 'Run workflow'.
{image?fileName=Galaxy_workflow_run_with_inputs.png}


##Submit the workflow

Prior to submission you must first read through the [Synapse tutorial](https://www.synapse.org/#!Help:GettingStarted) and pass the [Certification quiz](https://www.synapse.org/#!Quiz:Certification) to become a Certified User.

You must also create a new project in your profile for your submission that will act as your workspace in Synapse. To do this go to your profile on the [Synapse website](https://www.synapse.org/). Once there select the Projects tab, enter the name of your new project, and click 'Create Project'. You will use the project ID for this project later when you are completing the submission form for the Challenge.

To submit your workflow go to <External IP address>/submit/ where <External IP address> is the IP address associated with your VM instance (the IP address of your copy of Galaxy).
This IP address can be found in your either:
* in the URL of your Galaxy instance or
* in your [Google Developers Console](https://console.developers.google.com) by going to Compute -> Compute Engine -> VM instances and looking at the External IP column for your VM instance.

${image?fileName=Galaxy_submit.png}

> Currently there is no way of accessing the submission form from Galaxy directly

Once you are on the submit page select the workflow that you would like to submit and click 'Start Submission'. This will bring you to the submission form for SMC-Het:

${image?fileName=Galaxy_submission_form.png}

At the top of this form you will need to fill out three fields with information from your Synapse account:
* Your Synapse email (found in your profile page on Synapse under Settings)
* Your Synapse API Key (found in your profile page on Synapse under Setting by clicking 'Show API Key')
* The Synapse Project ID for your submission project created above (found in the top-left corner of your submission projects Synapse page under Synapse ID)

You will then need to fill out a short survey on the details of your algorithm. This information will be used to aid in post-challenge analysis and will be incredibly useful for future projects that use the results of the Challenge.

Once you have completed the survey click the 'Submit' button at the bottom of the page to complete your submission.