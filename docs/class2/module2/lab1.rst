
    
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
   
Build an run a docker container with ansible playbooks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In the following steps you will build and run a Docker container called agility2018 which will have Ansible installed. From the Docker container, you will provide the required configuration and authentication credentials to deploy an application into the Azure environment in a fully automated way. 

#. Clone the github repository to the Linux Host

   - git clone https://github.com/ajgerace/azure-f5 
   - View the contents of the directory. Note the addition of a subdirectory called 
   - ls 
   - Change directory to the azure-f5
   - cd azure-f5
#. Build Docker container (hint: note the period at the end of the command.  (A space is required after the period for this command to work)

   - sudo docker build -t agility2018
   - This step will take about 10 minutes
   - Verify that the agility2018 container exists and look at the other docker containers currently on the system
   - sudo docker images
   - Run the Docker container
   - sudo docker run -it --rm agility2018
   - Note the change at the prompt. You are now working inside the Docker container
   - Prompt is now /home/ansible
   
#. Clone the github repository to the Docker container (we use different components of the repository inside the container) and build a system using the existing Ansible playbook

   - git clone https://github.com/ajgerace/azure-f5
   - Create environment variables utilizing the student ID and password provided by the instructor
   - export AZURE_USERNAME=x-student#@f5custlabs.onmicrosoft.com
   - export AZURE_PW=ChangeMeNow123
   - Run bash script to create the Azure Service Principal and Secret
   - ./spCreate.sh
   - Output will look something like.....

  |image201|

#. Create the group_vars/all/vault.yml file with the variables in the black section and verify the contents

   - vi group_vars/all/vault.yml 
   - Paste the azure variables created in step 5 in and save the file
   - Delete the empty line between azure_tenant_id ad azure_user
   - Save - Write the vault.yml file
   - <esc>:wq
   - cat group_vars/all/vault.yml
   - **Troubleshooting tip---If all the values do not populate, the service principal was not created correctly or already exists. If this happens, access the Azure portal to delete the Service Principal for your student ID**
    - Login to Azure Portal
    - https://portal.azure.com 
    - USERNAME: x-student#@f5custlabs.onmicrosoft.com
    - Password: ChangeMeNow123
    - Click on Azure Active Directory
    - Click App registration
    - Click on your app  (studentX-app)
    - Click delete

    - Create the vault password file. This file will hold the vault password so that you will not have to input the password on the command line or be prompted for the password when running the playbook.
   - echo "@g!l!+y2018" > .vault-pass.txt
   - Encrypt the vault.yml file
   - ansible-vault encrypt group_vars/all/vault.yml
   - View the encrypted vault.yml file 
   - cat group_vars/all/vault.yml
   - View the contents of the encrypted vault.yml file 
   - ansible-vault view group_vars/all/vault.yml
   - View the contents of group_vars/azure-f5.yml. Note the prefix variable and the various IP addresses. This is the variable input file to the ansible playbook. 
   - 2.2. Run Ansible playbook with deploy_state=present to create deployment
   - ansible-playbook f5agility.yml -e deploy_state=present
   - This step will take about 20 minutes
   - Once complete review the comments on the screen. 
    - Note the URI for BIG-IP management
    - Note the URI for the VIP which was created
   |image202|


.. |image3| image:: /_static/class1/image3.png
   :width: 3.40625in
   :height: 4.04167in
.. |image201| image:: /_static/class1/image201.png
   :width: 4.40625in
   :height:2.04167in
.. |image202| image:: /_static/class1/image202.png
   :width: 5.40625in
   :height: 10.04167in
.. |image103| image:: /_static/class1/image103.png
   :width: 3.40625in
   :height: 1.04167in
.. |image104| image:: /_static/class1/image6.png
   :width: 5.40625in
   :height: 3.04167in
.. |image105| image:: /_static/class1/image105.png
   :width: 4.94792in
   :height: 6.20833in
.. |image106| image:: /_static/class1/image106.png
   :width: 6.32292in
   :height: 3.05208in
