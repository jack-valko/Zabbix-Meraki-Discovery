# Zabbix Meraki Discovery
Monitor all of your Meraki devices with Zabbix using the Meraki API and SNMP.  This template uses the script feature in Zabbix 5.2 and later to perform the heavy lifting against the Meraki JSON API to discover your organizations, networks, and devices.

## What's new
##### December 4, 2021
* BUG: SNMP Interfaces template is overdeployed (issue #11), verified, fix in development
* BUG: Template can't discover very large meraki organizations due to RangeError (issue #12), verified, fix in development

##### November 28, 2021
* Verified working on Zabbix 6 Alpha 7 

##### July 30, 2021
* Attempted bug fix for issue #3, cannot read property 'toLowerCase' of null 

##### June 29, 2021
* Now discovers MX250 gateways
* Alerts if a Meraki device hasn't checked into the API for over 5 minutes

## What's inside
The xml contains three objects, a Cloud Template (*Meraki Cloud Service*), a Device Template (*Meraki Device*), and a Host (*Meraki Cloud*).
* *Meraki Cloud Service* - This template contains the script code to contact the Meraki API and discover your organization(s), network(s), and device(s).  It contains a Host Prototype object for each discovered device.
* *Meraki Device* - This template is assigned to each discovered Meraki device, it will run additional discovery on the device to determine type and may create additional items.
* *Meraki Cloud* - This host can be assigned to a Zabbix server or proxy of your choice.  You'll add your API key here (see below).  The only template applied to this host is the *Meraki Cloud Service* template.  This layout allows independent development of the template and flexibility to assign this workload within your Zabbix environment as you see fit.

After discovery each device will be added to a new hostgroup in the form of *"Meraki Organization Name/Meraki Network Name"*.  Each host is also added to a new hostgroup "Discovered Meraki Devices".

## Prerequisites
* Zabbix 5.2 or 5.4
* echo.something in *externalscripts* directory
* Template 'ICMP Ping'
* Template 'Interfaces SNMP'
* [Your Meraki API Key](https://documentation.meraki.com/General_Administration/Other_Topics/Cisco_Meraki_Dashboard_API#Enable_API_access)
* [Enable SNMP in your Meraki environment in Network-wide > General > Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/SNMP_Overview_and_Configuration#Configuration)
* [Set your {$SNMP_COMMUNITY} macro value in the Zabbix UI](https://www.zabbix.com/documentation/current/en/manual/web_interface/frontend_sections/administration/general#macros) to the same string you entered in the previous step

## Zabbix 5.2 Install
##### This release is obsolete and unsupported.  Please upgrade to Zabbix 5.4.
1. Download Zabbix-Meraki-Discovery.xml from this repo
2. In the Zabbix UI, navigate to Configuration > Templates.  Click Import.
3. Click the 'Choose File' button and select your download of Zabbix-Meraki-Discovery.xml. __You must also click the checkbox to Create New Hosts__
4. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/zabbix-meraki-template-import.jpeg)

## Zabbix 5.4 Install
1. Download Zabbix-Meraki-Discovery.xml from this repo
2. In the Zabbix UI, navigate to Configuration > Templates.  Click Import.
3. Click the 'Choose File' button and select your download of Zabbix-Meraki-Discovery.xml
4. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/zabbix-54-template-import.jpeg)

5. A confirmation widget will pop-up with a summary of changes.  Click the 'Import' button again to confirm. 

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/zabbix-54-template-import-confirm.jpeg)

6. In the Zabbix UI, navigate to Configuration > Hosts.  Click Import.
7. Click the 'Choose File' button and select your download of Zabbix-Meraki-Discovery.xml
8. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/zabbix-54-host-import.jpeg)

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

## Monitoring Meraki devices with Interfaces SNMP
The templates uses SNMP to monitor individual Meraki devices, it does not use the Meraki Cloud SNMP. You should keep in mind:
1. Your {$SNMP_COMMUNITY} macro must match the SNMP string you entered into your Meraki Dashboard in [Network-wide > General > Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/SNMP_Overview_and_Configuration#Configuration)
2. Not all Meraki devices support SNMP, the template attempts to take this into account
3. The network between your Meraki devices and your Zabbix server or proxy will need to allow UDP 161 (snmp)
4. Your meraki devices will need to have unique LAN ip addresses

