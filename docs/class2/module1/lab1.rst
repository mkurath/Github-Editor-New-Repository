
Lab 1.1: Deploy a BYOL BIG-IP in azure with 3 NIC’s
==================================

   Task 1 – Access Github and deploy BIG-IP Azure Template 
-----------------------------------------------------------

In this lab you will build an F5 BIG-IP using a publicly available github template and a web server using the Azure portal GUI.  Once these components are built you will create a Virtual server and pool on the BIG-IP and verify connectivity to the Ubuntu server through the VIP.  Take time to inspect the objects in the Azure Resource Group you create. Azure provides integrated NAT and Firewall services. You will review the objects in the resource group through the portal to understand the IP scheme, NAT and Firewall rules.

Log on to the Ravello jumphost using the FQDN assigned by the instructor. All work in this lab will be done from the jumphost using the Browser and Terminal functions. 


|image3|

Accessing Ravello Jump Host

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
.. |image101| image:: /_static/class1/image101.png
   :width: 5.40625in
   :height: 6.04167in

#. You will be redirected to portal.azure.com]
   -Log into the azure portal when prompted
   -Username : x-student#@f5custlabs.onmicrosoft.com
   -Password:  ChangeMeNow123

#. Complete the Customized template with the following values (don’t follow the screen shot)

   +------------------------+---------------------+
   | Resource Group         | Select Create New   |
   +------------------------+---------------------+
   | Resource Group         | x-student#-rg       |
   +------------------------+---------------------+
   | Location               | East US             |
   +------------------------+---------------------+
   | Admin Username         | azureuser           |
   +------------------------+---------------------+
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
   
#. Azure GUI Screenshot

   - test

   |image102|
.. |image102| image:: /_static/class1/image102.png
   :width: 5.40625in
   :height: 10.04167in
   

#. Press the **Finished** button

View the objects which were created by the iApp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Select the Components tab at the top of the page

   |image6|

#. Is the Virtual server available?

#. Are the pool members available?

#. What is the node status? Why?

#. Note that a persistence profile was created

   - Check Match Across Services

   - Press update

   - Note the error at the top of the page

#. Return to iApp>>Application Services

#. Review the remaining parameters (any questions)

View the properties of the iApp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Select the Properties tab at the top of the page

   |image7|

#. Use the pull down next to Application Service:

#. Select Advanced

#. Note the check in Strict Updates

   - Is this related to the screen when editing the persistence profile?

   - What are the pro’s and con’s of unchecking this parameter?

Test the connection server load balancing using both VMware View client and browser access methods. 
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

.. |image3| image:: /_static/class1/image5.png
   :width: 5.40625in
   :height: 3.04167in
.. |image4| image:: /_static/class1/image6.png
   :width: 2.47015in
   :height: 1.73397in
.. |image5| image:: /_static/class1/image7.png
   :width: 4.94792in
   :height: 3.20833in
.. |image6| image:: /_static/class1/image8.png
   :width: 3.32292in
   :height: 1.05208in
.. |image7| image:: /_static/class1/image9.png
   :width: 3.15625in
   :height: 1.29167in
.. |image8| image:: /_static/class1/image10.png
   :width: 5.25000in
   :height: 3.18750in
.. |image9| image:: /_static/class1/image11.png
   :width: 1.29861in
   :height: 1.88819in
.. |image10| image:: /_static/class1/image12.png
   :width: 4.63542in
   :height: 3.06250in
.. |image11| image:: /_static/class1/image13.png
   :width: 5.67708in
   :height: 3.35417in
.. |image99| image:: /_static/class1/image5.png
   :width: 5.40625in
   :height: 3.04167in
