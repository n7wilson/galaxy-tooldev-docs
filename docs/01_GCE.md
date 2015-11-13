

Google Compute Engine
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
    3. Installed the gcloud command line tool (instructions in 3.5.10 - FAQ)

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

# Google Compute Engine Credits

Google Cloud Engine provides all new users with $300 USD of computing credits upon registration which are valid over two months (https://cloud.google.com/free-trial/) . All participants should first use these credits when working on the challenge. Teams may be able to maximize the use of these free credits by staggering when each member begins their free trial and sharing work through Github or similar platforms.  However, if teams run out of credits before the challenge ends they can contact the organizers about receiving further credits provided by Google.  See https://cloud.google.com/compute/pricing#machinetype for details on GCE pricing.