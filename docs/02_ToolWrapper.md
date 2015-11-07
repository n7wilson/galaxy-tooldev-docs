
${toc}

In addition to a runtime environment, which has been defined by Docker, each tool needs an XML file that defines all the parameters that will be needed to run the tool.
Each XML file should specify:
1. The tool name, id and version number
2. Environmental requirements, ie the name of the Docker container
3. Input files and input parameters
4. Output files
5. A command line function to be executed in the container for the given inputs and outputs

> The command line function can be the main file for the program you have written, if this file can be called from the command line. However it is more common to use a wrapper script that takes command line input, formats it, and then calls your program. Details on how to write a wrapper script are found below.

> Note on naming: the XML file used with your Galaxy tool is often called a 'Galaxy Tool Wrapper', however we will refrain from using that terminology to avoid confusion between a Galaxy XML file and a wrapper script (written in a scripting language) used to execute your program in Galaxy.

Optional Sections to include in the XML file
1. Configuration file creation
2. Unit tests
3. Documentation and help

More information on what you can include in an XML file can be found in the __Syntax for Galaxy XML File__ below under Tutorials and Docs.


Basic XML Template
--------

If your tool is fairly simple you can use the XML template here instead of creating your own:

```
<tool id="simple_tool" name="My Simple Tool" version="1.0.0">
  <!--
  To use this template:
  1) Change the tool id above to a unique string
  2) Write a wrapper to your program that takes 3 arguments (in this order):
    - The VCF file
    - The CNA file
    - The output file
  3) Change the 'my-program' in the requirements -> container section
  4) If your container is not in the Docker registry (https://registry.hub.docker.com/), create a Dockerfile
  5) Change 'my_program' in the command section to the name of your program
    (if the program is not in the PATH, use the full path of the program)
  
  This program template only outputs a single file, so if you wish to submit to multiple 
  sub-challenges, replicate the 'OUTPUT_FILE_1' in the command line and the 
  'outputs' section, or make one tool template per subchallenge
  -->
	<description>A simple example</description>
	<requirements>
		<container type="docker">my-program</container>
	</requirements>
	<command>
my_program ${VCF_FILE} ${CNA_FILE} ${OUTPUT_FILE_1}
	</command>

	<inputs>
		<param format="vcf" name="VCF_FILE" type="data" label="VCF file" help="" />
    <param format="txt" name="CNA_FILE" type="data" label="CNA file" help="" />
	</inputs>

	<outputs>
		<data format="txt" name="OUTPUT_FILE_1" label="Output File"/>
	</outputs>

	<help>
You should totally explain how to use your tool here
	</help>

</tool>

```
You can also download this template file directly: ${previewattachment?fileName=smc-het-xml-template.xml}

Follow the instructions at the top of the file to modify this template for your own tool.

##Writing Your Own XML File
###Tutorials and Docs
There are a number of resources you can find around the web for writing XML files for Galaxy and even more for writing XML files in general. Here are two that will help you get started:

__How to Write a Galxay XML File__
[http://www.slideshare.net/pjacock/galaxy-tools](http://www.slideshare.net/pjacock/galaxy-tools)

__Syntax for Galaxy XML File__
[https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax](https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax)

__Sample XML File Template__
[https://wiki.galaxyproject.org/Tools/SampleToolTemplate?action=show&redirect=Admin%2FTools%2FExampleXMLFile](https://wiki.galaxyproject.org/Tools/SampleToolTemplate?action=show&redirect=Admin%2FTools%2FExampleXMLFile)

If you are looking for more resources you can search the [Galaxy wiki page](https://wiki.galaxyproject.org/FrontPage)

###XML File Stanzas
Every XML file has certain stanzas that are required to fully describe how to interface with the tool:

1. Header (<tool>) - tool name, tool ID and tool version
2. Requirements (<requirements>) - declaration of the software requirements needed to run the tool. In the case of the SMC-Het challenge, this must be a declaration of a Docker container
3. Command Line Call (<command>) - a template for the command line that will be filled out by a templating engine at run time to container the correct parameter values and file paths.
4. Inputs (<inputs>) - All of the input parameters and files that will be passed to the program
5. Outputs (<outputs>) - All of the files that will be outputted by the program

You can also include other stanzas on top of these if you wish. In particular, it is a good idea to include the <help> stanza with information on the usage of your tool.

###Example XML File
Both of the example Galaxy tools that are provided in the [SMC-Het-Challenge package](https://github.com/Sage-Bionetworks/SMC-Het-Challenge-Examples) contain Galaxy XML files that you can use as examples.

The [DPC example](https://github.com/Sage-Bionetworks/SMC-Het-Challenge-Examples/blob/master/dpc/dpc.xml) is the simpler of the two:
```
<tool id="dpc" name="VCF DPC" version="1.0.0">
	<description>VCF clustering</description>
	<requirements>
		<container type="docker">r-base</container>
	</requirements>
	<command interpreter="Rscript">
dpc.R ${input_vcf} ${sample_number}
	</command>

	<inputs>
		<param format="vcf" name="input_vcf" type="data" label="VCF file" help="" />
		<param type="integer" name="sample_number" label="Sample number" help="The ith sample column in the VCF file" value="1"/>
	</inputs>

	<outputs>
		<data format="png" name="output_png" label="DirichletProcessplotBinomial PNG" from_work_dir="DirichletProcessplotBinomial.png"/>
		<data format="txt" name="cellularity" label="Cellularity (Sub Challenge 1A)" from_work_dir="subchallenge1A.txt"/>
		<data format="txt" name="no_clusters" label="Number Clusters (Sub Challenge 1B)" from_work_dir="subchallenge1B.txt"/>
		<data format="txt" name="proportions" label="Cluster Proportions (Sub Challenge 1C)" from_work_dir="subchallenge1C.txt"/>
		<data format="txt" name="assignments" label="Cluster Assignments (Sub Challenge 2A)" from_work_dir="subchallenge2A.txt"/>
		<data format="txt" name="co_clustering" label="Co-Cluster (Sub Challenge 2B)" from_work_dir="subchallenge2B.txt"/>
	</outputs>

	<help>
You should totally explain how to use your tool here
	</help>

	<tests>
		<test>
		</test>
	</tests>

</tool>
```

We can see that all of the information mentioned above is defined in this XML file:

The Header:
```
<tool id="dpc" name="VCF DPC" version="1.0.0">
```

The Requirements:
```
  <requirements>
    <container type="docker">r-base</container>
  </requirements>
```

The Command Line Call (using a wrapper script, dpc.R, to call the tools main function)
```
  <command interpreter="Rscript">
    dpc.R ${input_vcf}
  </command>
```

The Inputs (a vcf file and an integer specifying which sample in the vcf file to use)
_Notice that the 'name' attribute of each input corresponds to the names used in the Command Line Call above_
```
  <inputs>
    <param format="vcf" name="input_vcf" type="data" label="VCF file" help="" />
    <param type="integer" name="sample_number" label="Sample number" help="The ith sample column in the VCF file" value="1"/>
  </inputs>
```

The Outputs (a picture of the Dirichlet process from executing the tool and five text files for input into the scoring evaluator)
```
  <outputs>
    <data format="png" name="output_png" label="DirichletProcessplotBinomial PNG" from_work_dir="DirichletProcessplotBinomial.png"/>
    <data format="txt" name="cellularity" label="Cellularity (Sub Challenge 1A)" from_work_dir="subchallenge1A.txt"/>
    <data format="txt" name="no_clusters" label="Number Clusters (Sub Challenge 1B)" from_work_dir="subchallenge1B.txt"/>
    <data format="txt" name="proportions" label="Cluster Proportions (Sub Challenge 1C)" from_work_dir="subchallenge1C.txt"/>
    <data format="txt" name="assignments" label="Cluster Assignments (Sub Challenge 2A)" from_work_dir="subchallenge2A.txt"/>
    <data format="txt" name="co_clustering" label="Co-Cluster (Sub Challenge 2B)" from_work_dir="subchallenge2B.txt"/>
  </outputs>
```

###Command Line Call in an XML
The Command Line Call section of your XML file is where you actually execute your function. There are two aspects of the Command Line Call that are important to remember:
1. The Command Line Call uses a templating language called Cheetah to pass variables from your inputs into your command.In Cheetah all variables are identified using `${variable_name}`.

This should be everything most people need to know to write their XML files but for more information on Cheetah you can find documentation at:

http://www.cheetahtemplate.org/
http://www.devshed.com/c/a/Python/Templating-with-Cheetah/
http://www.onlamp.com/pub/a/python/2005/01/13/cheetah.html

2. Your Command Line Call should contain only one function call. If you need to call multiple functions you can either use a wrapper script or create an XML file for each function call and manually pass data between functions in Galaxy. For an example of how to do this check out the PhyloWGS tool that is provided in SMC-Het_challenge-Examples.

###Input For Your Tool
All the input files that you specify must come from your Galaxy history. This means that they must either be imported from the given training sets, imported from your local machine, or the result of running another tool. For more information on importing data check out [3.5.0 Quick Start](https://www.synapse.org/#!Synapse:syn2786217/wiki/266669)

###Output From Your Tool
All the output files that you specify will be saved to your Galaxy history. This makes passing data from one tool to another very easy, as the data will already be in your Galaxy history, where you need it.

###Parallelizing Your Tool
Galaxy will automatically pick the number of threads to use when trying to parallelize your tool but you can if you want you can specify the number of thread to use using the GALAXY_SLOTS environmental variable:
```
-nct \${GALAXY_SLOTS: N_THREADS}
```
where N_THREADS is your desired number of thread. GALAXY_SLOTS is set to -4 by default, and this will tell Galaxy to choose the number of threads for you.

_Note: the '\$', as the dollar sign character is passed on as a literal to the script (and not evaluated by the template system) because `$GALAXY_SLOTS` is actually an environmental variable defined at runtime, and not a variable that is filled in by the templating engine._

For more notes on parallel process settings see [http://galacticengineer.blogspot.co.uk/2015/04/using-galaxyslots-for-multithreaded_22.html](http://galacticengineer.blogspot.co.uk/2015/04/using-galaxyslots-for-multithreaded_22.html)


###Catching Errors
See the [Galaxy Tool Configuration docs](https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax), especially the section on the [&lt;stdio&gt;,
&lt;regex&gt; and &lt;exit_code&gt; tags](https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax#A.3Cstdio.3E.2C_.3Cregex.3E.2C_and_.3Cexit_code.3E_tag_sets)
 for details on how to configure error reporting within a Galaxy tool wrapper. The standard method is to look for error exit code.
 Note, if you are using a BASH script, use the 'set -e' so that any command error will result in the script failing. You can set up a regex to search stderr for failure messages, but this method should not be used by itself, as it may not catch all errors.

#Wrapper Scripts
Wrapper scripts are programs used to "wrap up" your functions and make them easier to execute. These scripts are executed in the command line. They usually take a number of input variables and fromat them to be passed into a function. Wrapper scripts can be written in a number of different coding languages including SH, Bash, Python, Perl and R among many others.

For more information check out these resources:

[http://mywiki.wooledge.org/WrapperScript](http://mywiki.wooledge.org/WrapperScript)
[http://tldp.org/LDP/abs/html/wrapper.html](http://tldp.org/LDP/abs/html/wrapper.html)
