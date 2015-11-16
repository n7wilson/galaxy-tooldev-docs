This section will tell you everything you need to get your VM set up and get you started building your tools! For more details on the check out the videos in [3.5.1 - Tutorials](https://www.synapse.org/#!Synapse:syn2786217/wiki/213548) and the other pages in this section.

In this section you will learn how to:
- Create a virtual machine (VM) via Google Compute Engine with your own Galaxy instance
- Setup the evaluator tool and the example tools

Google Cloud Platform Quick Start
---------------------------------

### 1. Setup Your Google Account and Create Your Project
_Note: this setup uses the Beta version of the Google Developers Console. If you are using the old version the instructions will be slightly different._

First go to https://console.developers.google.com and sign in with your Google account. You will be brought to the Google Developers Console (also known as the Google Compute Engine or GCE), the home page for setting up and managing your Virtual Machines through Google.

${image?fileName=Google_First_Login.png}

Once you have logged in to your Google Developer Console you need to sign up for a free trial by clicking the big blue button in the top righthand corner. This will require you to enter in credit card information but NO CHARGES WILL BE MADE unless you decide to upgrade your account before the end of the trial.
_Note: the free trial lasts for 60 days or 300$ worth of credits, after which we will be providing credits to each team for use in the rest of the Challenge. For more details see the Google Compute Engine Credits section in 3.5.2 - Google Compute Engine_

After signing up for the free trial Google will automatically create a project for you, which you can rename by going to the projects drop down list titled 'My First Project', selecting 'Manage all projects' and the clicking the rename button for your project.

${image?fileName=Google_projects_list.png}

${image?fileName=Google_manage_projects.png}

Once you have renamed your project go to the Home page, which should look like this:

${image?fileName=Google_Developer_Home_with_project.png}

For Google Compute Engine, a Project is an area to organize members, access cloud resources such as VM instances, and review your billing.  Every GCE project has an ID and number. You can choose your own ID, but the number will be assigned automatically by GCE.

To build your VM instance you will first need to create an image that your VM instance will be built from and a data disk that will be used as storage.

### 2. Load the Planemo Image Into Your Project.

Next you will need to load the planemo image, the image we have provided for all Challenge participants with the necessary software to  by clicking the Gallery button in the top lefthand corner
${image?fileName=gallery_button.png}
and then going to Compute -> Compute Engine. This will initialize your Compute Engine, which will take a few minutes.
_Note: you can check the status of any actions you have performed in GCE by looking in the 'Activities' panel in the bottom righthand corner_

${image?fileName=Google_activities_panel.png}

> If you receive a message saying there was an 'Unknown Error' refresh your browser, go back the the Compute Engine. If you are brought to the VM instances page then everything has completed correctly.

Once this is finished select Images in the panel on the lefthand side and click 'New Image' at the top of the page. Change the following values in the form and then press "Create"

    * Name: planemo-machine-image
    * Source type: Cloud Storage file
    * Cloud Storage file: `galaxyproject_images/planemo_machine_smc.01.image.tar.gz`
    _Note: the current version number for the planemo instance (in this case 01) will change throughout the Challenge as we updated the image._

> If you receive an error when creating your image first check to see if you are using the correct version. Any version updates will be changed here and posted in 1 - Challenge News and Updates


${image?fileName=Google_Developer_Create_Image.png}

### 3. Create Your Data Disk.

Now we need to create the disk to store your data on. In your Compute Engine select Disks in the panel on the lefthand side and click 'Create a disk'.

Change the following information in the form then press "Create" (as you did before).
    * Name: planemo-data
    * Zone: choose your preferred zone but be sure to make note of it and use the same zone when creating your VM
    * Source type: None (blank disk)
    * Size: we recommend at least 30GB

${image?fileName=Google_Developer_Create_Disk.png}

### 4. Create Your VM.

    Now you can actually build your VM Instance. From you Compute Engine select VM Instances in the panel on the lefthand side and click 'Create Instance'.
    Fill out the instance creation dialog with the following changes:
        * Name: planemo
        * Zone: same as the one you chose for the data disk
        * Machine Type: your choice, but a system with at least 6GB of RAM is expected (click Change to modify)
        * Boot Disk: Planemo Image you created previously but with more memory than the default
                    - click Change and select "Your image"
                    - select the image listed
                    - change the size of the to be at least 30GB using the Size field at the bottom of the window
    ${image?fileName=GCE_boot_disk_select.png}
        * Allow HTTP and HTTPS traffic
        * Data Disk: add the data disk you created previously
                    - Click on the 'Management, disk, networking, access & security options' to expand the drop-down.
                    - Click on Disks -> Add Item and select the disk you created in the prior step.

     Once you're done, hit 'Create'. It should take a few minutes for the VM to be created.

${image?fileName=Google_Create_Instance1.png}
${image?fileName=Google_Create_Instance2.png}


### 5. SSH to Your VM.

    Once your VM has been created navigate back to VM Instances then click the 'SSH' button at the bottom under the 'Connect' header.

${image?fileName=Google_Developer_SSH.png}

    This will open the command line for your VM instance in a new window.

${image?fileName=Google_VM_commandline.png}

### 6. Format and Mount your Data Disk.

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
$ gce_mount_tool_disk
```

> A 'bad magic error' at this step is typical, you can ignore it

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

You will be prompted for a username and password to access Galaxy. Enter the following information:
Username: planemo
Password: planemo

### 10. Import Data.

All the tumour data that you will need for the Challenge will be accessible directly from Galaxy. To import the data:
* Go to Data Libraries under the Shared Data tab at the top of your Galaxy page
* Select 'Imported Data'
* Check the box next to Tumour1 and click 'Go'
${image?fileName=Galaxy_import_data.png}
* All the data will now appear in your history
${image?fileName=Galaxy_tumour1_imported.png}

### 11. Run the example tool.

    1. Select the DPC tool in the left hand tool panel under Imported Tools

${image?fileName=Galaxy_Tool_menu.png}

    2. In the 'VCF file' field select 'Tumour1.mutect.vcf'
${image?fileName=Galaxy_DPC_input.png}
    3. Click the Execute button at the bottom of the form
    4. The output data files should appear in the data history panel on the right hand side of the screen
    _Note: The DPC tool takes a fairly long time to run (>30min). As long as the tool has not thrown an error then it should be working properly._

${image?fileName=Galaxy_DPC_Results_edit.png}

### 12. Evaluate the results
_Note: this will only evaluate the algorithm results based on the training sample that you used, this will NOT submit your results to the Challenge_

Now that you have some sample data to submit to the Challenge you can run it through the evaluator and see how well you did.

    1. Select the Evaluator Tool in the left hand tool panel
${image?fileName=Galaxy_evaluator.png}
    2. For each of the inputs for subchallenge 1 and 2 select the corresponding results file from your history _Note: the DPC tool does not provide results for subchallenge 3._
${image?fileName=submit_dpc.png}

    3. Click the Execute button at the bottom of the form
${image?fileName=Galaxy_evaluator_execute_button.png}
    4. Look in your history in the right hand panel and check your results!
    You can see the results be clicking the 'View data' icon
${image?fileName=view_data_button.png}
    which will show you a dictionary with the scores for each subchallenge that you submitted to. Scores are all between 0 and 1 with 1 being a perfect score and 0 being...less perfect.

Ta da!  You have set up your Google Compute engine virtual machine running Galaxy, run a sample tool and used the evaluator to test your results. Now you are ready to start making your own tools and testing them in Galaxy!