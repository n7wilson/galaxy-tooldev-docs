
We've provided some example Galaxy tools to give you an idea of how to develop tools for the Challenge. Feel free to download these tools and play around with them to get a better sense of how to use Galaxy, Docker and the rest of the Challenge resources.

## Download example code
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

The DPC tool is the simpler of the two examples, since it only uses one Galaxy tool and does not require you to build the Docker image for the tools yourself, so it is advisable to start with this one.

To use the DPC tool head to your Galaxy instance and import the Training data:
1. Select the DPC tool in the left hand tool panel
2. Find the 'mutect.vcf' VCF file in the 'VCF file' selection in the center panel
3. Click the `Execute` button at the bottom of the form

The once the tool has run the output data will be found in your history.

> If you get the following error: `/var/run/docker.sock: no such file or directory.` `Are you trying to connect to a TLS-enabled daemon without TLS?`, try restarting the docker daemon by running the following command in your VM instance: `sudo service docker restart`.

## Using the PhyloWGS Example
TODO: Finish this section

INPUT:
VCF File
CNA File

OUTPUT:
Submission files for subchallenges 1A, 1B, 1C, 2A, 2B, 3A and 3B

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

### Tool 1: Extracting the CNA

To use the DPC tool head to your Galaxy instance and import the Training data:
1. Select the DPC tool in the left hand tool panel
2. Find the 'mutect.vcf' VCF file in the 'VCF file' selection in the center panel
3. Click the `Execute` button at the bottom of the form

The once the tool has run the output data will be found in your history.

> If you get the following error: `/var/run/docker.sock: no such file or directory.` `Are you trying to connect to a TLS-enabled daemon without TLS?`, try restarting the docker daemon by running the following command in your VM instance: `sudo service docker restart`.
