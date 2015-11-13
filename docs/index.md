

Galaxy Tools and Workflows are an integral part of the Challenge. All of your analysis using the Challenge data should be using Galaxy
Tools since all submissions to the Challenge will require you to create a Galaxy Workflow. This section contains all the information on setting the software development process for the Challenge.
This includes setting up your working environment, developing your Galaxy tools, testing your tools, creating Galaxy workflows, and submitting your workflows to the Challenge. It also includes some extra information like a Glossary and an FAQ section.


# Galaxy Tool Development

There are three different software platforms that you will use in the Challenge:
* [Google Compute Engine](https://console.developers.google.com/) (for your Virtual Machine)
* [Docker](http://docker.io) (for setting up your tool's dependencies), and
* [Galaxy](https://usegalaxy.org/) (for developing and testing your algorithm)

Information on using all three of these platforms can be found in this section.

# Setting Up Your Environment - The Planemo Machine

Planemo-Machine is a Galaxy Tool Standard Development Kit (SDK) designed to
make developing Docker based Galaxy tools easy. It has an installed copy of Galaxy,
an up to date version of Docker, a web based IDE and the Planemo tools.

With this system, which can be deployed on a variety of virtual machine services,
you can quickly develop and test new tools and see how they behave in the Galaxy environment.

For the Challenge we have developed our own version of the Planemo-Machine that contains everything you will need to work on the Challenge. The rest of this section contains details on how to use the Planemo-Machine with the three software platforms mentioned above, Google Compute Engine, Docker and Galaxy.

From here the first thing you should do is go through 3.5.0 - Quick Start to get your environment setup. After that, if you are looking for more information on any one of the software platforms above you can check out the specific sections here or look in 3.5.10 - FAQ. Finally you can also check out the details on submitting to the Challenge in 3.5.7 - Submitting to the Chellenge and 3.5.8 - Submission File Format.
