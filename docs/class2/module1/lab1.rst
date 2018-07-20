       

Lab 1.1: Deploy a BYOL BIG-IP in azure with 3 NIC’s
==================================

   Task 1 – Access Github and deploy BIG-IP Azure Template 
-----------------------------------------------------------

In this lab you will build an F5 BIG-IP using a publicly available github template and a web server using the Azure portal GUI.  Once these components are built you will create a Virtual server and pool on the BIG-IP and verify connectivity to the Ubuntu server through the VIP.  Take time to inspect the objects in the Azure Resource Group you create. Azure provides integrated NAT and Firewall services. You will review the objects in the resource group through the portal to understand the IP scheme, NAT and Firewall rules.

Log on to the Ravello jumphost using the FQDN assigned by the instructor. All work in this lab will be done from the jumphost using the Browser and Terminal functions. 

   |image3|

Accessing Ravello Jump Host
~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. When prompted for credentials

   - Username: Ubuntu
   - Password: supernetops
   - Open the chrome browser
   - Adjust the font size using the Zoom In
   - Size the window
#. Browse to Github to access the F5 – Azure templates

   - https://github.com/F5Networks/f5-azure-arm-templates
   - Scroll down to the List of F5 ARM templates for Azure deployments to the section titled Deploying the BIG-IP VE in Azure - 3 NICs
   - Click PAYG Deploy to Azure
   |image101|

#. You will be redirected to portal.azure.com]
   -Log into the azure portal when prompted
   -Username : x-student#@f5custlabs.onmicrosoft.com
   -Password:  ChangeMeNow123

#. Complete the Customized template with the following values **(don’t follow the screen shot)**

   +------------------------+---------------------+
   | Resource Group         | Select Create New   |
   +------------------------+---------------------+
   | Resource Group         | x-student#-rg       |
   +------------------------+---------------------+
   | Location               | East US             |
   +------------------------+---------------------+
   | Admin Username         | azureuser           |   +------------------------+---------------------+
   | Admin Password         | ChangeMeNow123      |
   +------------------------+---------------------+
   | DNS Label              | x-student#BIGIP     |
   +------------------------+---------------------+
   | Licensed Bandwidth     | 25M                 |
   +------------------------+---------------------+
   | Number of External IPs | 3                   |                      
   +------------------------+---------------------+
   |Timezone                | UTC                 |
   +------------------------+---------------------+ 
 
#. Check the “I Agree” box in front of the terms and conditions
#. Select the “Purchase” button

   |image102|
#. This will take about 15 minutes –
   - You can monitor deployment on the azure dashboard by opening the Notifications in the azure portal

   |image103|





#. Continue with the Lab. The deployment will complete by the time the BIG-IP configuration is required


Install a Linux Server in Azure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Navigate to the Azure Marketplace and select Create a Resource
#. Select Ubuntu Server 17.10 VM

   |image104|

#. Complete the Customized template with the following values **(don’t follow the screen shot)**

   +------------------------+---------------------+
   | Name                   | F5Ubuntux-student#  |
   +------------------------+---------------------+
   | VM disk type           | HDD                 |
   +------------------------+---------------------+
   | Admin Username         | azureuser           |
   +------------------------+---------------------+
   | Admin Password         | ChangeMeNow123      |
   +------------------------+---------------------+
   | Resource Group         | Select:Use Existing |
   +------------------------+---------------------+
   | Resource Group         | x-student#-rg       |
   +------------------------+---------------------+
   | Location               | East US             |                      
   +------------------------+---------------------+
   |Timezone                | UTC                 |
   +------------------------+---------------------+ 

#. Select the “OK” button

   |image105|
   
#. Select the machine type

   - Highlight B1s
   - Select Button at the bottom of the page

   |image106|

#. Define the machine config parameters

   - Select Subnet
   - Select the internal subnet
   - Select SSH in the select public inbound ports
   - Select the “OK” button

   |image107|

#. Create the machine
 
   - Review the configuration
   - Select the “Create” button

   |IMAGE108|

Install Apache Web Server on the Linux Server in Azure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#. Access the Azure Portal to find the external IP address of the Ubuntu Server

   - Resource Groups
   - Select your Resource Group
   - Identify the Object with the Ubuntu Public IP address

   |image109|

#. SSH to the Apache Server 

   - Open the Terminal window on the jumphost
   - ssh  azureuser@<Ubuntu public IP Address>
   - Password: ChangeMeNow123
   
#. Use the following Commands to install Apache Web server

   - sudo apt-get update
   - sudo apt-get install apache2


Use the Azure portal to gather IP information about the systems you have built
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#. Access the Azure Portal to find the IP address on the internal network of the Ubuntu Server

   - Resource Groups
   - Select your Resource Group
   - Identify the object with the Ubuntu Network Interface 
   - Click the "Add" button
   - Click the "finished" button
   - Note the IP-Address <10.0.3.5>

   |image110|

#. Access the Azure Portal to find the public IP address assigned to the F5 management interface.
 
   - Resource Groups
   - Select your Resource Group
   - Identify the Object with the BIG-IP Management Interface x-student#-mgmt
   - Note Public IP mapped to the management interface

   |image111|

#. Access the Azure Portal to find the NAT IP address assigned to the external F5 interface. 

   - Resource Groups
   - Select your Resource Group
   - Identify the Object with the BIG-IP External Interface x-student#bigip-ext
   - Select IP configurations in the left panel
   - Note External Self IP mapped to 10.0.2.4
   - Note External Self IP mapped to 10.0.2.10 (this will be used to access the VIP created on the BIG-IP)

   |image112|

Review the BIG IP config objects created by the template and build a VIP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



#. Access the BIG-IP management GUI

   - https://<Public-IP-of-Management>
   - Username: azureuser
   - Password: ChangeMeNow123

#. Inspect the configuration of the BIG-IP

   - The github template has built the base configuration 
   - System>>License
   - Network>>Self IPs
   - Network>>VLANs

#. Create a pool with the Ubuntu Server as a member (Note that we only created a single web server. Typically there would be multiple members in the pool)

   - Local Traffic>>Pools
   - Create Button in upper right corner

   +------------------------+----------------------------------------+
   | Name                   | Azure_Ubuntu_Pool                      |
   +------------------------+----------------------------------------+
   | Health Monitors        | http                                   |
   +------------------------+----------------------------------------+
   | Address                | 10.0.3.5  <IP Info from Azure Portal>  |
   +------------------------+----------------------------------------+
   | Service Port           | http                                   |
   +------------------------+----------------------------------------+
   
   - Click the "Add" button
   - Click the "finished" button


   |image113|
   
   #. Create a Virtual Server using the Azure_Ubuntu_Pool

      - Local Traffic>>Virtual Servers
      - Create Button in upper right corner

   +---------------------------------------------+---------------------------------------+
   | Name                                        | Azure_Ubuntu_VIP                      +
   +---------------------------------------------+---------------------------------------+
   | Address                                     | 10.0.2.10 <IP Info From Azure Portal> |
   +---------------------------------------------+---------------------------------------|
   | Service Port                                | http                                  |
   +---------------------------------------------+---------------------------------------+
   | HTTP Profile                                | http                                  |
   =---------------------------------------------+---------------------------------------+

   |image114|



 Disregard everything below this line --- except image definitions at bottom
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 








#.  From "corporate-pc"

#.  Open View client and connect to the Virtual Server just created with
    iApp.

    - \+ New Server

      - ``vmw-LB-CS.demoisfun.net``

      - Connect Button

        - IP address will not work—Certificate contains demoisfun.net

#.  When prompted for credentials

    - Username: ``demo01``

    - Password: ``password``

    - Login Button

#.  Double-click Agility icon to launch View desktop

#.  Verify that the Agility desktop functions

#.  Close the View client. (May need to slide the RDP Toolbar out of the

way)

#.  Open IE and browse to ``https://vmw-LB-CS.demoisfun.net``

#.  Select VMware Horizon View HTML access

#.  Log in

    - Username: ``demo01``

    - Password: ``password``

#.  Double click to launch Agility desktop

#.  At the Cert Warning, click "Continue to this website"

#.  Verify that the Agility desktop functions

#.  Close the IE browser window

Task 3 – Access View Desktop through Security Server
----------------------------------------------------

Test the functional VMware View environment using external Security
Servers. (External use case without F5 integration)

This environment shows a user connecting to a native VMware security
server which is statically mapped to a VMware connection server. This is
a non-redundant external access model

|image8|

Figure 4 - Access external View Desktop

#.  From the "home-pc"

    |image9|

#.  If you are using an existing VMW unfrastructure, it is possible to load balance the Connection servers contacted by the UAG server. We do this by using the VIP created in step 1 in the UAG configuration. No configuration is required by the student. (this parameter is pre configured) Get the Thumbprint by inspecting the details of the certificate when you access the VIP with a browser

    |image99|

#.  Use the VMware Horizon View client to access the security server

    - \+ New Server

    - Security Server address ``vmw-uag1a.demoisfun.net``

    - Press Connect Button

#.  When prompted for credentials

    - Username: ``demo01``

    - Password: ``password``

#.  Double-click Agility icon to launch desktop

#.  Close the View client

#.  Access the application through your browser 
    ``https://vmw-uag1a.demoisfun.net``

#.  Select VMware Horizon View HTML access

    - Username: ``demo01``

    - Password: ``password``

#.  Double-click Agility icon to launch desktop

#.  Accept Cert at warning

#.  Select (Agility)

#.  Verify that the desktop functions

#.  Close the browser window

Task 4 – Load Balance Security Servers
--------------------------------------

Use the F5 iApp for VMware View to configure a load balancing
environment for the Security Servers. This will increase the number of
Security Servers available to internal users and load balance access to
these resources (External use case with F5 load balancing)

This environment load balances 2 external facing Security Servers. These
Security Servers are directly mapped to 2 existing connection servers in
the environment (not the 2 Connections Servers that are load balances in

the steps above)

|image10|

Figure 5 - Load balance Security Servers

**Deploy the iApp**

#. From "corporate-pc"
0#. Create a new Application Service by selecting

   - iApps >> Application Services

   - Press the **Create** button

   - Name the Application Service ``VM_LAB_1_LBUAG``

   - Select ``f5.vmware_view.v1.5.1`` for the template

#. Review the **Welcome to the iAPP template for VMware Horizon View**

#. Note the **Template Options** (leave these default)

#. **Big-IP Access Policy Manager** (Set this to **No** for this
   exercise)

#. **SSL Encryption** (Certs are preloaded for this exercise)

   +----------------------------------------------------------+--------------------------------------------------------------+
   | How should the BIG-IP system handle encrypted traffic?   | Terminate SSL for clients, re-encrypt…\ **(SSL-Bridging)**   |
   +==========================================================+==============================================================+
   | Which SSL certificate do you want to use?                | wild.demoisfun.net.crt                                       |
   +----------------------------------------------------------+--------------------------------------------------------------+
   | Which SSL private key do you want to use?                | wild.demoisfun.net.key                                       |
   +----------------------------------------------------------+--------------------------------------------------------------+

#. **PC Over IP** (leave these default – No PCoIP connections…)

#. **Virtual Servers and Pools**

   +------------------------------------------------------------------------------------+---------------------------+
   | What virtual server IP address do you want to use for remote, untrusted clients?   | 192.168.3.150             |
   +====================================================================================+===========================+
   | What FQDN will clients use to access the View environment?                         | vmw-LB-SS.demoisfun.net   |
   +------------------------------------------------------------------------------------+---------------------------+
   | Which Servers should be included in this pool?                                     | 192.168.3.210             |
   |                                                                                    |                           |
   |                                                                                    | 192.168.3.211             |
   +------------------------------------------------------------------------------------+---------------------------+

#

.. |image101| image:: /_static/class1/image101.png
   :width: 5.40625in
   :height: 6.04167in
.. |image102| image:: /_static/class1/image102.png
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
.. |image107| image:: /_static/class1/image107.png
   :width: 6.15625in
   :height: 6.29167in
.. |image108| image:: /_static/class1/image108.png
   :width: 6.25000in
   :height: 6.18750in
.. |image109| image:: /_static/class1/image109.png
   :width: 6.29861in
   :height: 6.88819in
.. |image110| image:: /_static/class1/image110.png
   :width: 6.63542in
   :height: 5.06250in
.. |image111| image:: /_static/class1/image111.png
   :width: 6.67708in
   :height: 5.35417in
.. |image112| image:: /_static/class1/image112.png
   :width: 6.67708in
   :height: 7.35417in
.. |image113| image:: /_static/class1/image113.png
   :width: 6.67708in
   :height: 5.35417in
.. |image114| image:: /_static/class1/image114.png
   :width: 6.67708in
   :height: 9.35417in
.. |image3| image:: /_static/class1/image3.png
   :width: 5.40625in
   :height: 3.04167in
