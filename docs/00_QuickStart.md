This section will tell you everything you need to get your VM set up and get you started building your tools! For more details on the check out the videos in [3.5.1 - Tutorials](https://www.synapse.org/#!Synapse:syn2786217/wiki/213548) and the other pages in this section.

In this section you will learn how to:
- Create a virtual machine (VM) via Google Compute Engine with your own Galaxy instance
- Setup the evaluator tool and the example tools

Google Cloud Platform Quick Start
---------------------------------

### 1. Create Your Project

First go to https://console.developers.google.com to view your projects. If you do not already have a project use the 'Create Project' button.


${image?fileName=Google_Developer_Create_Project.png}

Once you have created your project go to the Home page, which should look like this:

${image?fileName=Google_Developer_Home.png}

For Google Cloud Platform, a Project is an area to organize members, cloud resources such as GCE instances, and billing.  Every GCP project has an ID and number. You can choose your own ID, but the number will be assigned automatically by GCP.

### Load the Planemo Image Into Your Project.

Next you will need to load the planemo image by going to Compute -> Compute Engine -> Images -> New Image in the left hand menu. Change the following values in the form and then press "Create"

    * Name: planemo-machine-image
    * Source type: Cloud storage object
    * Cloud storage object path: `galaxyproject_images/planemo_machine_smc.image.tar.gz`

${image?fileName=Google_Developer_Create_Image.png}

###3. Create your data disk.

Now we need to create the disk to store your data on. Go to  Compute -> Compute Engine -> Disks -> New Disk in the menu and change the following information in the form then press "Create" (as you did before).

    * Name: planemo-data
    * Zone: choose your preferred zone but be sure to make note of it and use the same zone when creating your VM
    * Source type: None (blank disk)
    * Size: we recommend at least 30GB

${image?fileName=Google_Developer_Create_Disk.png}

### 4. Create your VM.

    Head to Compute -> Compute Engine -> VM Instances. If a dialog pops up asking what you want to do, select 'Create Instance', otherwise click the 'New Instance' button.
    Fill out the instance creation dialog with the following changes:
        * Name: planemo
        * Zone: same as the one you chose for the data disk
        * Machine Type: your choice, but a system with at least 6GB of RAM is expected (click Change to modify)
        * Boot Disk: image you created previously with more memory than the default
                    - click Change and select "Your image"
                    - select the image listed
                    - change the size of the to be at least 30GB using the Size field at the bottom of the window
    ${image?fileName=GCE_boot_disk_select.png}
        * Allow HTTP and HTTPS traffic
        * Data Disk: add the data disk you created previously
                    - Click on the 'Management, disk, networking, access & security options' to expand the drop-down.
                    - Click on Disks -> Add Item and select the disk you created in the prior step.

     Once you're done, hit 'Create'

${image?fileName=GCE_Launch.png}


### 5. SSH to the newly created VM.

    To see your instance list, navigate back to Compute -> Compute Engine -> VM Instances then click the 'SSH' button at the bottom under the 'Connect' header.

${image?fileName=Google_Developer_SSH.png}

### 6. Format and mount your data disk.

    We do all of the commands that follow as user `ubuntu` on the VM.  Change to this user.
```
$ sudo su ubuntu
```
> You must execute this command every time you SSH into you VM instance. If you encounter any errors while developing always check to ensure that you are working on the ubuntu user first.

    Run the following command to find the disk and make sure it is properly installed. There should be 3 files found: < VM instance >, < VM instance >-data, and < VM-instance >-part1
```
$ ls -l /dev/disk/by-id/google-*

lrwxrwxrwx 1 root root  9 May  8 23:02 /dev/disk/by-id/google-planemo ->  ../../sda
lrwxrwxrwx 1 root root 10 May  8 23:02 /dev/disk/by-id/google-planemo-data -> ../../sda1
lrwxrwxrwx 1 root root  9 May  8 23:03 /dev/disk/by-id/google-planemo-part1 -> ../../sdb
```

    Make sure the `/opt/galaxy/tools` directory is empty.
```
$ ls -l /opt/galaxy/tools

total 0
```
    Mount and format the disk.
```
$ sudo /usr/share/google/safe_format_and_mount -m "mkfs.ext4 -F" /dev/sdb /opt/galaxy/tools
```

    Set ownership of the disk to user `ubuntu`.
```
$ sudo chown -R ubuntu /opt/galaxy/tools
```

    Now your external disk should be mounted by the VM.
```
$ df -h

Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  3.0G   26G  11% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
udev            3.7G   12K  3.7G   1% /dev
tmpfs           749M  412K  748M   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            3.7G     0  3.7G   0% /run/shm
none            100M     0  100M   0% /run/user
/dev/sdb        493G   70M  467G   1% /opt/galaxy/tools
```

> Be sure that the first Filesystem in this list is at least 30GB in size. If not, return to Step 4, create a new VM instance, and ensure that you change the size of your Boot Disk to 30GB (or more).

### 7. Install the SMC-Het Examples on your VM.

Run the following commands in you VM:
```
$ cd /opt/galaxy/tools
$ git clone https://github.com/Sage-Bionetworks/SMC-Het-Challenge-Examples.git
$ cd SMC-Het-Challenge-Examples
```

### 8. Restart Galaxy and Docker.

Run the following commands in your VM:
```
$ sudo service docker restart
$ restart_galaxy
```

### 9. Connect to Galaxy

Go back to the Developers Console and click the little pop-out arrow to the right of the external IP address for the instance.  Your browser will be redirected the the home page for Galaxy on your VM.

${image?fileName=Galaxy_Home_Page.png}

### 10. Import Data.

All the tumour data that you will need for the Challenge will be accessible directly from Galaxy. To import the data:
* Go to Data Libraries under the Shared Data tab at the top of your Galaxy page
* Select 'Imported Data'
* Check the box next to Tumour1 and click 'Go'
${image?fileName=Galaxy_import_data.png}
* All the data will now appear in your history
${image?fileName=Galaxy_tumour1_imported.png}

TODO: Find the correct aspot for this section (if any)
    There are two ways to upload data to galaxy: from your local machine or from a website URL

    1. Local Data:
        * Click the upload button
        ${image?fileName=upload_button.png}
        * Click 'Choose local file'
        * Select the desired file
    2. Online Data:
        * Click the upload button
        ${image?fileName=upload_button.png}
        * Click 'Paste/Fetch Data'
        * Input the URL of the data file

    For this example we will use the 'test_vcf.vcf' file found in the SMC-Het-Challenge github at 'https://raw.githubusercontent.com/Sage-Bionetworks/SMC-Het-Challenge/master/data/test_vcf.vcf'

${image?fileName=Galaxy_upload_Data.png}

### 11. Run the example tool.

    1. Select the DPC tool in the left hand tool panel

${image?fileName=Galaxy_DPC_Tool.png}

    2. In the 'VCF file' field select 'Tumour1.mutect.vcf'
${image?fileName=Galaxy_DPC_input.png}
    3. Click the Execute button at the bottom of the form
    4. The output data files should appear in the data history panel on the right hand side of the screen
    _Note: The DPC tool takes a fairly long time to run. As long as the tool has not thrown an error than it should be working properly._

${image?fileName=Galaxy_DPC_Results.png}

### 12. Evaluate the results

Now that you have some sample data to submit to the Challenge you can run it through the evaluator and see how well you did.

    1. Select the Evaluator Tool in the left hand tool panel
${image?fileName=Galaxy_evaluator.png}
    2. For each of the inputs for subchallenge 1 and 2 select the corresponding results file from your history _Note: the DPC tool does not provide results for subchallenge 3._
${image?fileName=Galaxy_evaluator_complete.png}

    3. Click the Execute button at the bottom of the form
${image?fileName=Galaxy_evaluator_execute_button.png}
    4. Look in your history in the right hand panel and check your results!

Ta da!  You have set up your Google Compute engine virtual machine running Galaxy, run a sample tool and used the evaluator to test your results. Now you are ready to start making your own tools and testing them in Galaxy!