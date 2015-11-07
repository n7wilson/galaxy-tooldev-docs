
Submitting a workflow to the SMC-Het Challenge
==============================================

${toc}

A submission to the challenge is a [Galaxy workflow](https://wiki.galaxyproject.org/Learn/AdvancedWorkflow). Submitting the workflow is done by the `dream_galaxy_submit` script found in the GitHub repo [SMC-Het-Challenge](https://github.com/Sage-Bionetworks/SMC-Het-Challenge). This script will scan a Galaxy workflow, download all of the relevant tools and then upload them to your Synapse Project.


In Galaxy, workflows are created in one of two ways. The first way is to extract a workflow from the 'History', then edit it to create a streamlined workflow. The second is to create the workflow from scratch in the [workflow editor](https://wiki.galaxyproject.org/Learn/AdvancedWorkflow/BasicEditing/WorkflowEditorUnannotated).

##Extracting a Workflow From Your History
Galaxy lets you perform data analysis operations, keeping a history of the tools you run and their parameters. To extract a workflow from your history:
1. Select the 'History Options' drop down menu on the right hand of the Analysis panel above the history, which looks like a gear.
2. Click on 'Extract Workflow', which will give you the option of creating a workflow based on your previous actions.
3. Clean up the workflow in the Galaxy Editor so that it follows the requirements for submitting to the Challenge

##Creating a Workflow From Scratch
If you don't want to use your history to create a workflow you can also build one from scratch. To do this:
1. Select the Workflow menu at the top of the page
2. Once in the Workflow section, select 'Create new Workflow'
3. Name your workflow and hit the 'Create' button
4. Once in the workflow editor, find the tools you want to include in the panel on the lefthand side and click them to create add them to your workflow in the central panel
7. Create inputs to the workflow by selecting Inputs -> Input dataset at the bottom of the panel on the lefthand side
5. Connect the tools and input data in the order you want to execute them by dragging 'connection noodles' from the outputs on the right hand side of one tool/dataset to the inputs on the left hand side of the next tool
6. Edit the tool/dataset names and parameters in the panel on the righthand side
7. Save your workflow by clicking Settings (the gear button in the top righthand corner of the central pane) -> Save
TODO: Add in pictures to show how to do this with the PHyloWGS tool
TODO: add the PhyloWGS workflow to Galaxy


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

Test the workflow
-----------------

Submit the workflow
-------------------

The code for submitting code to the challenge can be found at https://github.com/Sage-Bionetworks/SMC-Het-Challenge to install:
```
git clone https://github.com/Sage-Bionetworks/SMC-Het-Challenge.git
```

This code includes the script `dream_galaxy_submit` which will scan a workflow URL stored on a Galaxy instance, download the relevant tools and data, and then upload them to a Synapse directory.

The first step is to get the download URL of the workflow. To find it:

1. Go to the `Workflow` panel
2. Click the name of the workflow to be submitted
3. When the dropdown menu appears select 'Share or Publish'
4. If it exists, click the button `Make Workflow Accessible via Link`
5. Below the text `Anyone can view and import this workflow by visiting the following URL` there will be the text of a URL. This is the URL you will provide to the submission program


Next you will need your Galaxy API key:
1. On the Top 'User' menu, select 'API Keys'
2. Copy the value in the 'Current API key' field
3. If the value is 'none set', then click `Generate a new key now` and copy the produced value

You can now run the `dream_galaxy_submit` program
```
./dream_galaxy_submit \
--workflow http://128.114.61.142:9999/u/dev/w/smc-het-file \
--apikey ec713e37df8cdf5e9ffdd97c5c56bab1 --project-id syn2751753
```
