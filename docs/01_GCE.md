

Google Compute Engine based Deployment
======================================

${toc}

This section will help you set up a compute instance and a persistent disk containing challenge code and data. You can choose to use either the Developers Console web interface (covered in [3.5.0 Quick Start](https://www.synapse.org/#!Synapse:syn2786217/wiki/266669)) or the Google Cloud SDK command line tool (covered below). If you need more help getting started with using Google Compute Engine, [the user documentation](https://cloud.google.com/compute/docs/quickstart-developer-console) provides a good intro/summary of how to use the console.

The main steps to set up your environment are:
1. importing the planemo image
2. creating a persistent disk big enough to hold your code and the data
3. creating an instance and attach the disk
4. mounting and formating the disk
5. testing your Galaxy instance by running a sample workflow

# Setting up your Working Environment via the Command Line
_Note: All commands should be run from your local machine_

Before you start make sure you have:
    1. Created a [Google Developer account](http://console.developers.google.com/) (you can use your Google/Gmail account)
    2. Created a project to work on the Challenge through your [Google Developer Console](https://console.developers.google.com/project)
    3. Installed the gcloud command line tool (instructions in the FAQ below)

### Load the Planemo Image

The first step is to load the planemo image that your VM instances will be built on. To do this run the following command:
```
gcloud compute images create planemo-machine-image --source-uri http://storage.googleapis.com/galaxyproject_images/planemo_machine_smc.image.tar.gz
```
If this runs successfully you should see the following output:
```
Created [https://www.googleapis.com/compute/v1/projects/level-elevator-666/global/images/planemo-machine-image].
NAME                  PROJECT            ALIAS DEPRECATED STATUS
planemo-machine-image level-elevator-666                  READY
```
### Allow your VM Instance through your Firewall
Before you create your VM instance you may need to add a firewall rule set to allow traffic to the HTTP port (80).
To add an HTTP ruleset run the following command:
```
gcloud compute firewall-rules create allow-http \
    --target-tag http-server \
    --description "Incoming http allowed." --allow tcp:80
```
This will assign the tagset name 'http-server', which will be referenced later when we create an VM instance that uses these firewall rules.
_More information on using Google Developer with firewalls can be found in the GCE [quick start](https://cloud.google.com/compute/docs/networks-and-firewalls) manual._

### Create a VM Instance
To create and start running a VM instance based off of your planemo image run the following command:
```
gcloud compute instances create planemo \
    --machine-type n1-standard-2 --image planemo-machine-image \
    --tags http-server
```
If this command executes properly it will return the following output:
```
Created [https://www.googleapis.com/compute/v1/projects/level-elevator-666/zones/us-central1-f/instances/planemo].
NAME    ZONE          MACHINE_TYPE  INTERNAL_IP    EXTERNAL_IP    STATUS
planemo us-central1-f n1-standard-2 10.240.143.115 162.222.182.19 RUNNING
```
Now if you navigate to the listed EXTERNAL_IP, you will find the running Planemo-Machine

### SSH into you VM Instance
To SSH into the machine run the following command:
```
gcloud compute ssh ubuntu@planemo
```
where `planemo` is the name of the instance you created earlier.

> If you go to the web page and you see an error that 'an internal server error' has occured, and the message doesn't go away, you can restart the server by sshing into the server and running the command `sudo supervisorctl restart galaxy:`

> Remember when you are done using your VM, turn it off. You are charged for every hour it is on, and if you forget about it, it will rack up costs quickly.

### Attach additional storage to the VM

If you need more storage for your VM you can create a volume to attach to your machine by running the following command on your local machine:
```
gcloud compute disks create --size 30GB planemo-data
```

Once you have created the disk you can attach it to your instance by running the following command:
```
gcloud compute instances attach-disk --disk planemo-data planemo
```
After the disk is attached to the instance, it will be available to be mounted.

### Setup your VM Instance

From here follow the instructions in the [Quick Start](https://www.synapse.org/#!Synapse:syn2786217/wiki/266669), starting from Step 6.



# Frequently Asked Questions

## How can I save my work?
Create [snapshots](https://cloud.google.com/compute/docs/disks/persistent-disks#snapshots) of your persistent disks.

## How can I make the most of my credits?
[Stop](https://cloud.google.com/compute/docs/instances/#stopping_an_instance) your virtual machine instances when not in use and [restart](https://cloud.google.com/compute/docs/instances/#restarting_a_stopped_instance) them when you are ready to work again.

## How can I see how much of my credits I have spent?
View your [cost and payment history](https://support.google.com/cloudbilling/answer/3540823?hl=en&ref_topic=2991962).

## How do I get support for Google Compute Engine
For any questions about Google Compute Engine, please post to the forum on [Stack Overflow](http://stackoverflow.com/questions/tagged/google-compute-engine)

For assistance from a Google Support Engineer, please see https://cloud.google.com/support/.

## How do I install and configure the gcloud command line tool?

#### Setup gcloud on your local machine
1. Find your Challenge project ID
Go to your [Google Developers Console](https://console.developers.google.com/project) to view your projects, and find your project ID under the 'Home' section. If you do not already have a project use the 'Create Project' button.

2. Install Google Cloud SDK on you local machine
Follow the instructions [here](https://developers.google.com/cloud/sdk/) to install the Google Cloud SDK on your local machine.
Note: all command line inputs need to be run on your local machine __not__ you VM instance.

#### Setup gcloud on your VM instances
3. SSH into the VM instance that you want to use gcloud on.

To do this find your VM instance on the Google Developer Console under Compute -> Compute Engine -> VM instances  at the bottom of the page. Once you have found your VM instance ensure that it is started (if it is not select the instance and click the 'Start' button at the top of the page). Finally click the 'SSH' button under the 'Connect' header at the bottom of the page.

4. Authenticate to gcloud.

Run the following command in your VM terminal:
```
gcloud auth login
```
and follow the on-screen instructions.

5. Configure the default project ID (if you did not do so in Step 4)

If you were not asked for your project ID in Step 4 then run the following command in you VM terminal to set your project ID.
```
gcloud config set project YOUR-PROJECT-ID
```
Recall that you can find your project ID in your [Google Developers Console](https://console.developers.google.com/project) under the 'Home' section.

> If you receive the following error during any of the later steps:
>  ######!ERROR: (gcloud.compute.instances.create) The required property [project] is not currently set.
>  ######!You may set it for your current workspace by running:
>
>  ######!$ gcloud config set project VALUE
>
>  ######!or it can be set temporarily by the environment variable [CLOUDSDK_CORE_PROJECT]
> your current project has not been set so you should execute the above command.

6. Configure the default zone

To avoid needing to select a time zone any time you run a gcloud command run the following command in your VM instance, replacing 'us-central1-f' with your desired time zone.
```
gcloud config set compute/zone us-central1-f
```
A list of time zones that are compatible with gcloud can be found [here](https://cloud.google.com/compute/docs/zones)

7. Enable the Compute Engine API

If you've never used GCE before, you may need to enable the Compute Engine API.  Click [here](https://console.developers.google.com/start/api?id=compute_component) to enable the API.

You can also enable the Compute Engine API manually by following these steps:
    1. go to your [Google Developers Console](https://console.developers.google.com/project)
    2. choose the project you are using for the Challenge
    3. Under 'APIs & auth' -> APIs, select 'Compute Engine API' and click the 'Enable' button at the top of the page

> If you receive the following error:
> ######!NAME PROJECT ALIAS DEPRECATED STATUS
> ######!ERROR: (gcloud.compute.images.create) Some requests did not succeed:
> 
> ######!Access Not Configured. The API (Compute Engine API) is not enabled for your project.
> ######!Please use the Google Developers Console to update your configuration.
> You have not enabled the Compute Engine API

## How can I update the Planemo Image?
If there is a need to update the SDK image, usually because bug fixes or new features, you will need to create an new image and a new VM instance based on that image.

To create the new image and VM instance follow the same instructions you used to create your original image and VM instance (above or in [3.5.0 Quick Start](https://www.synapse.org/#!Synapse:syn2786217/wiki/266669). All the instructions are the same except you will need to choose a new name for your image, your disk and your instance (for example 'planemo-v2' instead of 'planemo').

When updating your image, you may want to get delete the older version to save space. There is no need to keep an image if there is an instance running based on that image, since the instance does not depend on the image once it has been created. If you delete the image before setting up your new image you can also use the same name for the new image and the old image.

Deleting the image isn't required if you are willing to pay for the extra storage space, and can manage the names of multiple images.

Once you have created your new VM instance you can then transfer all the data from your old instance to your new instance, by following the instructions below, so you do not lose any of your work.

After you have successfully transfered your data from your old VM instance to your new VM instance you can delete your old VM instance if you want, to save space. 

>Make sure you transfer all the data from your old VM instance to either your local machine or your new VM instance before deleting your old VM instance or else you will lose all your work.

Alternatively, if you do not want to delete your old VM instance, you can stop it (but not delete it) by running the following command from your local machine:
```
gcloud compute instances stop planemo
```
You can also stop your instance manually by following these steps:
    1. go to your [Google Developers Console](https://console.developers.google.com/project)
    2. find your old VM under Compute -> Compute Engine -> VM instances at the bottom of the page
    3. select your old VM and click the 'Stop' button at the top of the page

##How do I delete my old image?

> Deleting an image is different then deleting an instance. The image is simply the start source, and once an instance as been started it is no longer dependent on the image. You work is stored in the instance. Do not delete that until you've copied it somewhere else.

To delete the old image run the following command from your local machine:
```
gcloud compute images delete OLD_IMAGE_NAME
```

If you do not have gcloud installed or prefer not to use the command line you can delete your old image manually by following these steps:
    1. go to your [Google Developers Console](https://console.developers.google.com/project)
    2. find your old image under Compute -> Compute Engine -> Images
    3. select your old image and click the 'Delete' button at the top of the page

##How can I transfer data between VM instances?
The easiest way to move data between two VMs is to use your personal machine as a transfer point. We will be transferring data from your old VM to your local machine and then from your local machine to your new VM.

_Note: For all instructions below replace `planemo` and `planemo-v2` with the names of your old and new VM instances respectively._
_Note: Before you start, ensure that you have set up gcloud on your local machine and both your VM's (old and new)._

* Start by running both instances at the same time. You can check if both instances are running and using gcloud by executing following the command on your local machine:
```
gcloud compute instances list
```
This should return the following list:
```
NAME       ZONE          MACHINE_TYPE  INTERNAL_IP   EXTERNAL_IP     STATUS
planemo-v2 us-central1-f n1-standard-2 10.240.229.40 162.222.179.9   RUNNING
planemo    us-central1-f n1-standard-2 10.240.49.28  130.211.131.162 RUNNING
```

* Next copy the files from the original machine to your local machine by executing the following command on your local machine:
```
gcloud compute copy-files ubuntu@planemo:/opt/galaxy/tools ./
```

* Finally copy the files from your local machine to the new VM by executing the following command on you local machine:
```
gcloud compute copy-files tools ubuntu@planemo-v2:/opt/galaxy/
```



