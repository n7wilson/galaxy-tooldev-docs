
Galaxy Tool Development
=======================

Since the Challenge is being run on the Cloud and uses Docker containers to setup the environment for your tools there are very few requirements for what you need to include in your tool.
The few requirements that we do have are:
1. Every submitted tool MUST define a docker container requirement. Other requirements mechanisms [documented in galaxy](https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax?action=show&redirect=Admin%2FTools%2FTool+Config+Syntax#A.3Crequirements.3E_tag_set), for example the 'package' system, are not supported for the challenge.
2. The Docker container reference in your tools XML file must either reference a valid image on the [Docker Registry](https://registry.hub.docker.com/) or be defined in a Dockerfile, defined by the user, in the tool package.
3. ToolData inputs in your tool's XML file, i.e. reference file paths specified by the `from_file` or `from_data_table` flags in [the options tag](https://wiki.galaxyproject.org/Admin/Tools/ToolConfigSyntax?action=show&redirect=Admin%2FTools%2FTool+Config+Syntax#A.3Coptions.3E_tag_set), are not allowed.

If you have technical questions about Galaxy you can look in the [BioStars](https://biostar.usegalaxy.org/) group or the contact the [Galaxy Dev mailing list](http://dev.list.galaxyproject.org/).

Tool Development Cycle
----------------------

1. Initialize Your Tool
In '/opt/galaxy/tools' run
```
planemo tool_init
```
2. Reload Galaxy
Run `restart_galaxy` anywhere in your VM. Your tool should now appear in the list of tools.
3. Create/Edit Your Dockerfile
_Note: if you are using a Docker container from the [Docker Registry](https://registry.hub.docker.com/) then you can skip steps 3 and 4._
Information on creating a Dockerfile can be found in [3.5.3 - Docker](https://www.synapse.org/#!Synapse:syn2786217/wiki/266673)
4. Build Your Docker Container
Run docker build in the directory with your Dockerfile to construct a working container:
```
planemo docker_build .
```
5. Edit Your Tool's XML File
Information on creating an XML File can be found in [3.5.4 - Tool XML File]()
You can also check the file syntax with
```
planemo lint my_cool_tool.xml
```
6. Test Your Tool in Galaxy
> If you think that Galaxy has not updated your tool use the command `sudo supervisoctl restart galaxy` or `restart_galaxy` and try again


Web Based IDE
-------------

Included in the Planemo VM is an installation of [Codebox](https://www.codebox.io/). You can find it by going to `http://<VM IP address>/ide/`

It automatically loads the the /opt/galaxy/tools directory, which is the same directory Galaxy scans to look for installed tools.

${image?fileName=codebox_ide.png}
