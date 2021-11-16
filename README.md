
# Splunk Enterprise Deployment

The files in this repository were used to configure the network depicted below.

 ![Diagram](Diagram/Azure_Diagram_Project_4.png)


## [**Azure**](https://azure.microsoft.com/en-us/)

*VM Specifications*
 - Linux (ubuntu 20.04) Operating System
 - 20_04-lts-gen2
 - Standard B1ms
 - 1 vCPU
 - 2 GiB RAM
 
These downloads have been tested and used to generate a live Splunk Enterprise deployment on Azure. They can be used to either recreate the deployment pictured above.

### [Splunk](https://www.splunk.com/)

### [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html)

### [Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder/thank-you-universalforwarder.html)



This document contains the following details:

-Description of the Topology

-Access Policies

-Splunk Enterprise Installation

-Azure Network Security Group

-Splunk Settings

-Splunk Universal Forwarder Installation

-Data Verification

-Target Machines

## Description of the Topology
#

The main purpose of this network is to have a secure environment and monitor a Ruby On Rails instance application.

- **Containerization was used to create a control node. This serves the purposed of security, elasticity, and scalability.**

- **Load balancing ensures that the application will be highly redundant and available, in addition to restricting access to the network.**

- **Load balancers protect the availability of the server. It reduce the attack vector on the back-end of the network.**

- **Integrating a Splunk Enterprise instance allows users to easily monitor the VM's for changes to the configuration files, system logs, performance, and much more.**

The configuration details of each machine may be found below.
| Name   | Function   | IP Address | Operating System |
|--------|------------|------------|------------------|
| Kirk   | Jump-box   | 10.0.0.4   | Linux            |
| Spock  | Web Server | 10.0.0.5   | Linux            |
| Scotty | Splunk     | 10.1.0.4   | Linux            |

## Access Policies
#
The machines on the internal network are not exposed to the public Internet. 
Only the Jump-box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP address: My personal IP address
-  Whitelisted (Personal IP address)

Machines within the network can only be accessed by the Jump-box through the control node SSH connection.

- I have allowed Kirk  (Jump-box) access to the Back-end Server Pool and Splunk Enterprise.

A summary of the access policies in place can be found in the table below.

| Name   | Publicly Accessible | Allowed IP Addresses |
|--------|---------------------|----------------------|
| Kirk   | Yes                 | Personal IP          |
| Spock  | No                  | 10.0.0.0-254         |
| Scotty | No                  | 10.1.0.0-254         |

## Splunk Enterprise Installation
#
Navigate to the /opt directory on the VM that Splunk Enterprise instance is to be installed.

*Download*
-
Run `sudo wget -O splunk-8.2.3-cd0848707637-linux-2.6-amd64.deb 'https://download.splunk.com/products/splunk/releases/8.2.3/linux/splunk-8.2.3-cd0848707637-linux-2.6-amd64.deb'`

[***Splunk Enterprise***](images/Splunk_Enterprise.png)

*Extract*
-
Run `sudo dpkg -i splunk-8.2.3-cd0848707637-linux-2.6-amd64.deb`

[***Splunk Enterprise Extraction***](Images/Splunk_Enterprise_Extract.png)

*Commands*
-
Run `cd /opt/splunk/bin`

Run `./splunk start --accept-licences`
	
*File Configuration*
-
Edit [splunk-launch.conf](Images/Splunk_Launch.png) file to bind IP Address.

Run `vi ../etc/splunk-launch.conf`

- Copy web.conf file. 

Run `cp /opt/splunk/etc/system/default/web.conf /opt/splunk/etc/system/local`

- Edit [web.conf](Images/Web_File.png)

Run `vi /opt/splunk/etc/system/local/web.conf`

- Uncomment *mgmtHostPort=121.0.0.1:8089*

- Edit IP address, it should be the same IP Address as in splunk-launch.conf file.
	
Run `./splunk restart`

## Azure Network Security Group
#
Add inbound security rule to Splunk Enterprise VM.
Allow [port 8000 and 8089](Images/Azure_NSG_Allow_Port_8000_8089.PNG)

Navigate to the Splunk Enterprise URL [IP Address:8000](Images/Splunk_Enterprise_Home.png) to check that it is running and operational.



## Splunk Settings
#
First click manage apps, than click Browse more apps.

- Search for Linux apps

- Install [Splunk Ad-on for Unix and Linux](Images/Splunk_apps.png)

Than click home.

Click settings drop down.

Click Forward and receiving.

First, click [Configure receiving](Images/Configure_receiving.png), than click New Recieving Port and enter port 9997.

## Splunk Universal Forwarder Installation
#
Navigate to the /opt directory on the VM that Splunk Universal Forwarder is to be installed.

*Download*
-
Run `sudo wget -O splunkforwarder-8.2.3-cd0848707637-linux-2.6-amd64.deb 'https://download.splunk.com/products/universalforwarder/releases/8.2.3/linux/splunkforwarder-8.2.3-cd0848707637-linux-2.6-amd64.deb'`

[Splunk Universal Forwarder](Images/Splunk_Universal_Forwarder.png)

*Extract*
-
Run `sudo dpkg -i splunkforwarder-8.2.3-cd0848707637-linux-2.6-amd64.deb`

[Splunk Universal Forwarder Extraction](Images/Splunk_Universal_Forwarder_Extraction)

*Commands*
-
Run `cd /opt/splunkforwarder/bin`
 
Next run `sudo ./splunk start --accept-licence`

Next run `sudo ./splunk add forward-server IP Address:9997`

Next run `sudo ./splunk set deploy-poll IP Address:8089`

Next run `sudo .splunk add monitor /var/log/`

Than run `sudo ./splunk restart`
	
[Start](Splunk_Universal_Forwarder_Start)

[Add and Set Forwarder](Images/Add_and_Set_Forwarder.png)

[Add Monitor](Images/Add_Monitor.png)

## Data Verification
#
Navigate to the Splunk Enterprise URL [IP Address:8000](Images/Splunk_Enterprise_Home.png) to check that it is running, and monitoring the Virtual Network.

Click settings drop down.

Next click [Forwarder management](Images/Forwarder_Management.png)

Next navigate back to [Splunk Enterprise Home](Images/Splunk_Enterprise_Home.png)

Than click Search & Reporting

Next click [Data Summary](Images/Data_Summary.png)

If you need to troubleshoot connection.

Run `sudo ./splunk list forward-server`

[list forward-server](Images/List_Forward_Server.png)
#
Lastly ensure Splunk Enterprise and the Universal Forwarder start on boot.

*Commands*
-
*Splunk Enterprise*

Run `sudo ./splunk enable boot-start`

[Splunk Enable](Images/Splunk_Enable.png)

*Splunk Universal Forwarder*

Run `sudo ./splunk enable boot-start`

[Splunk Enable](Images/Forwarder_Enable.png)



## Target Machines
#
The Splunk Universal Forwarder is installed and configured to monitor the following machines:
- 10.0.0.4
- 10.0.0.5


