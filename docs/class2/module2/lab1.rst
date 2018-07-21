
    
Lab 2.1: Introduction to F5 Orchestration with Ansible 
======================================================

In this lab we will start a docker container running ansible and use a playbook created in advance to create an environment in Azure. The specific components which will be created are a virtual network in Azure, spin up BIG-IP, configure the BIG-IP, and spin up a few Linux hosts do serve as pool members. The configuration will all be done from a Linux host in the Ravello Cloud. The location of the Linux host used for configuration does not need to be in Azure. It can be in your data center, in a docker container environment on a laptop, or in any cloud of your choice. The Linux host running the docker container for this lab will be accessed through Ravello and is actually running in AWS

The Linux host in the Ravello cloud is preloaded with Ansible, Python and Docker. 

Automation makes this a repeatable process as demonstrated by the entire class performing this same operation using the same ansible playbook. The selection of Ansible is arbitrary. Ansible uses the REST implementation to configure the BIG-IP and the other components in the environment. Many other tools can be used for orchestration including, but not limited to python, terraform, Puppet, and Chef. 

Log on to the Ravello jumphost using the FQDN assigned by the instructor. All work in this lab will be done from the jumphost using the Browser and Terminal functions. 

   |image3|

Accessing Ravello Jump Host and deploy BIG-IP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. When prompted for credentials

   - Username: Ubuntu
   - Password: supernetops
   - Open the chrome browser
   - Adjust the font size using the Zoom In
   - Size the window
#. Browse to Github to access the F5 – Azure templates

