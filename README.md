# Zabbix-Meraki-Discovery
Monitor all of your Meraki devices with Zabbix using the Meraki API and SNMP.  This template uses the script feature in Zabbix 5 to perform the heavy lifting against the Meraki JSON API to discover your organizations, networks, and devices.  

## What's inside
The xml contains three objects, a Cloud Template (*Meraki Cloud Service*), a Device Template (*Meraki Device*), and a Host (*Meraki Cloud*).
* *Meraki Cloud Service* - This template contains the script code to contact the Meraki API and discover your organization(s), network(s), and device(s).  It contains a Host Prototype object for each discovered device.
* *Meraki Device* - This template is assigned to each discovered Meraki device, it will run additional discovery on the device to determine type and will creates additional items.
* *Meraki Cloud* - This host can be assigned to a Zabbix server or proxy of your choice.  You'll add your API key here (see below).  The only template applied to this host is the *Meraki Cloud Service* template.  This layout allows independent development of the template and flexibility to assign this workload within your Zabbix environment as you see fit.

After discovery each device will be added to a new hostgroup in the form of *"Meraki Organization Name/Meraki Network Name"*.  Each host is also added to a new hostgroup "Discovered Meraki Devices".

## Prerequisites
* Zabbix 5.2
* echo.something in *externalscripts* directory
* Template 'ICMP Ping'
* Template 'Interfaces SNMP'
* [Your Meraki API Key](https://documentation.meraki.com/General_Administration/Other_Topics/Cisco_Meraki_Dashboard_API#Enable_API_access)
* [Enable SNMP in your Meraki environment in Network-wide > General > Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/SNMP_Overview_and_Configuration#Configuration)

## Install
1. Download Zabbix-Meraki-Discovery.xml from this repo
2. In the Zabbix UI, navigate to Configuration > Templates.  Click Import.
3. Click the 'Choose File' button and select your download of Zabbix-Meraki-Discovery.xml. __You must also click the checkbox to Create New Hosts__
4. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/zabbix-meraki-template-import.jpeg)

If any errors appear, congratulations!  You have an install I have not tested.  [Please file a bug.](https://github.com/jack-valko/Zabbix-Meraki-Discovery/issues/new)

## Configuration
1. In the Zabbix UI, navigate to Configuration > Hosts
2. Click on host 'Meraki Cloud'
3. Click on 'Macros'
4. Set a new value for {$APIKEY}, enter your Meraki API Key in the text field
5. Click the 'Update' button

## Running your first discovery
Discovery is designed not to burden the Meraki API with a lot of traffic, so it is set at a period of 12h.  You don't have to be this patient, run your first discovery by hand.
1. In the Zabbix UI, navigate to Configuration > Hosts
2. Click on host 'Meraki Cloud'
3. Click on 'Discovery rules'
4. Click on 'Meraki Device Discovery'
5. Click the 'Execute now' button at the bottom of the page

Running by hand will take a few minutes to complete and about an hour for all SNMP data to start trickling in.  
