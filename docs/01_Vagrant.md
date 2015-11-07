

# Advanced Topics


# Running Planemo On Your Personal Machine

As an alternative to the Google Cloud based development kit, it is possible to run the Planemo SDK on your personal machine. This will still involve using a VM but the VM will be running locally instead of in the cloud. For the purposes of the SMC-Het challenge, we provide this as an alternative but encourage contestants to use Google Cloud VMs and take advantage of the free compute credits provided.

> Using Planemo on your personal machine requires you to install both [VirtualBox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/)
> Be aware: building a new VM may take a long time

1. Download the latest version of the planemo appliance from https://images.galaxyproject.org/planemo/latest.box.

2. Download and install [Vagrant](http://www.vagrantup.com/downloads)

3. Enable vagrant by creating a `Vagrantfile` in your tool directory, which will configure your VM. An example Vagrantfile can be found below:

```
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
config.vm.box = "planemo"
config.vm.box_url = "https://images.galaxyproject.org/planemo/latest.box"

# Forward nginx.
config.vm.network "forwarded_port", guest: 80, host: 8010

# Disable default mount and mount pwd to /opt/galaxy/tools instead.
config.vm.synced_folder ".", "/vagrant", disabled: true
config.vm.synced_folder ".", "/opt/galaxy/tools", owner: "vagrant"

end
```
You can also download this example Vagrantfile directly: ${previewattachment?fileName=Vagrantfile}

> You Vagrantfile must be named `Vagrantfile`

4. Next you will need to startup the appliance. To do this open your terminal and execute the following command in the directory with you Vagrantfile in it:

```
vagrant up
```

5. Log into the Galaxy server by going to http://localhost:8010/ in your web browser


6. Access the command line inside the virtual machine by running the command:
```
vagrant ssh
```

7. Move to `/opt/galaxy/tools` and start working on your tool!
