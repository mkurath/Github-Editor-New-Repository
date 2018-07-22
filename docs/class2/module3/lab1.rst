Lab 3.1: Modify the Ansible configuration to re-deploy an HA pair of big-IP’s with the same application environment
============================

In this lab we will modify the existing Ansible playbook to create an HA environment in Azure. The specific components which will be created are a virtual network in Azure, 2 BIG-IP’s in HA configuration, configure the BIG-IPs, and spin up a few Linux hosts do serve as pool members. The configuration will all be done from a Linux host in the Ravello Cloud. 

This lab assumes that you have completed LAB 2 which includes build and run the docker container and creating the credential vault. 

#. Return to the Terminal window

   - Prompt is now /home/ansible/azure-f5
   - Change directory to /home/ansible
   - sudo docker ps -a (this will allow you to see the CONTAINER ID)
   - sudo docker exec -i  -t <CONTAINER ID> /bin/sh 

#. Edit group_vars/azure-f5.yml file to enable deployment of A/P BIG-IP cluster.

   - Change the following variables
   +----------------+------------------+-------------------+
   | Variable       | Existing Value   + New Value         |
   +================+==================+===================+
   | Deployment     | 2nic             | ha                |
   +----------------+------------------+-------------------+
   | extVipAddr1    | 10.10.10.246     | 10.10.1010        |
   +----------------+------------------+-------------------+

#. Let’s take a look at the Ansible Playbooks used to create the objects (BIG-IP, BIG-IP configuration, and Linux servers) instead of running the 2nic playbook, this deployment will run the bigip_ha playbook.

   - View the variable assignments in the group_vars/azure-f5.yml
   - cat group_vars/azure-f5.yml
   - View the f5agility.yml file. This is the Ansible code which controls the execution of the individual playbooks. Playbooks are referred to as roles in this file. 
   - cat f5agility.yml | more
   - View the directories where the playbooks are stored
   - ls
   - Inspect a one or more of the playbooks
   - cd bigip_ha/tasks
   - cat main.yml | more
   - cd /home/ansible/azure-f5
   
#. Re-run the Ansible playbook to create the new deployment. A message is displayed to console with the public IP addresses for the BIG-IP management interfaces as well as the virtual server.

   - ansible-playbook f5agility.yml -e deploy_state=present
   |image301|

#. Let’s take a look at the BIG-IP configurations which were created. Access both BIG-IP’s using the information provided in the final comments following the TASK deployment. You can also access this information in the resource group objects on the Azure portal. In this exercise, the main focus will be the HA configuration. 

   - Access BIG-IP Management interface
   - https://<BIG-IP-MGMT-IP-ADDRESS> (Obtain this info from the ansible output or the Azure portal)
   - Username: x-student#
   - Password: ChangeMeNow123
   - Determine which BIG-IP is active and perform the following on the GUI
   - Device Management>>Devices
   - Failover Objects
   - Note bodgeit_srvc_dscvry
   - Properties
   - Force to Standby
   - Confirm operation by selecting Force to Standby again
   
#. Note that while the HA configuration on the BIG-IP in Azure is very similar to traditional HA configurations in an on prem data center, the operation is different. In Azure, an Azure API is triggered when a failover occurs. The public IP associated with a VIP is only assigned to the external network on the active member of the HA pair. Failover time can be greater than 30 seconds when using this method 

FINAL GRADE
~~~~~~~~~~~
Thank you for participating this "F5 Azure Automation" lab. Please complete the **SURVEY** to
let us know how we did. We value your feedbacks and continuously looking
for ways to improve.

**THANK YOU FOR CHOOSING F5 !!!**

.. |image3| image:: /_static/class1/image3.png
   :width: 3.58333in
   :height:4.96875in
.. |image301| image:: /_static/class1/image301.png
   :width: 6.25126in
   :height: 2.65672in
.. |image17| image:: /_static/class1/image19.png
   :width: 3.28358in
   :height: 3.79055in
.. |image18| image:: /_static/class1/image20.png
   :width: 1.82813in
   :height: 1.68013in
.. |image19| image:: /_static/class1/image21.png
   :width: 5.25486in
   :height: 1.65269in
