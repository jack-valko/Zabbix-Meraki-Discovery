# Zabbix Meraki Discovery
Monitor all of your Meraki devices with Zabbix using the Meraki API and SNMP.  This template uses the script discovery feature in Zabbix 5.4 and later to perform the heavy lifting against the Meraki Dashboard API to discover your organizations, networks, and devices.

## What's new
##### June 27, 2022
* NEWS: Fix for template import error on 6.0 now [scheduled for the 6.0.7 release](https://support.zabbix.com/browse/ZBX-20699) (late July 2022)

##### May 26, 2022
* ENHANCEMENT: Discovery will log errors to zabbix server log
* ENHANCEMENT: Deprecated old curlHttpRequest()
* FIX: Discovery verifies API requests return usable data
* FIX: Discovery now replaces unsupported characters

##### May 24, 2022
* BUG: [Meraki devices with unsupported characters can't be created](https://github.com/jack-valko/Zabbix-Meraki-Discovery/issues/23)

##### March 15, 2022
* BUG: Template import broken in 6.0.1 and later, this template is only known to work on 5.4 production releases at this time

##### March 5, 2022
* BUG: [Template import is broken on Zabbix 6.0.1](https://support.zabbix.com/browse/ZBX-20699). Follow [issue #20](https://github.com/jack-valko/Zabbix-Meraki-Discovery/issues/20) for updates.
 
##### February 5, 2022
* Verified to work on Zabbix 6.0 RC2
 
##### January 22, 2022
* FIX: Improved support for large Meraki networks
* FIX: SNMP Interfaces template only added to Meraki devices that support SNMP
* FIX: Discovery now ignores offline organizations and devices
* ENHANCEMENT: Reduced load on Meraki API with caching
* FIX: Trigger warning on last check-in increased to 10 minutes
* ENHANCEMENT: Use of external scripts deprecated in favor of native Zabbix script
* Verified to work on Zabbix 6.0 Beta 1
* Support for Zabbix 5.2 has been dropped

##### 2021 Updates
* BUG: SNMP Interfaces template is overdeployed (issue #11), verified, fix in development
* BUG: Template can't discover very large meraki organizations due to RangeError (issue #12 and #16), verified, fix in development
* FIX: Attempted bug fix for issue #3, cannot read property 'toLowerCase' of null 
* ENHANCEMENT: Now discovers MX250 gateways
* ENHANCEMENT: Alerts if a Meraki device hasn't checked into the API for over 5 minutes

## What's Inside
The xml contains three objects, a Cloud Template (*Meraki Cloud Service*), a Device Template (*Meraki Device*), and a Host (*Meraki Cloud*).
* *Meraki Cloud Service* - This template contains the script code to contact the Meraki API and discover your organization(s), network(s), and device(s).  It contains a Host Prototype object for each discovered device.
* *Meraki Device* - This template is assigned to each discovered Meraki device, it will run additional discovery on the device to determine type and may create additional items.
* *Meraki Cloud* - This host can be assigned to a Zabbix server or proxy of your choice.  You'll add your API key here (see below).  The only template applied to this host is the *Meraki Cloud Service* template.  This layout allows independent development of the template and flexibility to assign this workload within your Zabbix environment as you see fit.

After discovery each device will be added to a new hostgroup in the form of *"Meraki Organization Name/Meraki Network Name"*.  Each host is also added to a new hostgroup "Discovered Meraki Devices".

## Prerequisites
* Zabbix 5.4, ~~6.0~~
* Template 'ICMP Ping'
* Template 'Interfaces SNMP'
* [Your Meraki API Key](https://documentation.meraki.com/General_Administration/Other_Topics/Cisco_Meraki_Dashboard_API#Enable_API_access)
* [Enable SNMP in your Meraki environment in Network-wide > General > Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/SNMP_Overview_and_Configuration#Configuration)
* [Set your {$SNMP_COMMUNITY} macro value in the Zabbix UI](https://www.zabbix.com/documentation/current/en/manual/web_interface/frontend_sections/administration/general#macros) to the same string you entered in the previous step

## Recommended Zabbix Server Configuration Parameters
If you have a large meraki fleet of 100+ networks and/or 100+ hosts, you will need to adjust some Zabbix parameters.  Your milage may vary depending on the number of devices in your configuration, in our testing we found these settings worked for a fleet of over 200 devices and over 100 networks:
```
CacheSize=512M 
StartPollers=15 
StartHTTPPollers=3
StartLLDProcessors=5
```

## Install
1. Download [Zabbix-Meraki-Discovery.xml](https://raw.githubusercontent.com/jack-valko/Zabbix-Meraki-Discovery/main/Zabbix-Meraki-Discovery.xml) for Zabbix 5.4.
2. In the Zabbix UI, navigate to Configuration > Templates.  Click Import.
3. Click the 'Choose File' button and select the file downloaded in step 1.
4. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/images/zabbix-54-template-import.jpeg)

5. A confirmation widget will pop-up with a summary of changes.  Click the 'Import' button again to confirm. 

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/images/zabbix-54-template-import-confirm.jpeg)

6. In the Zabbix UI, navigate to Configuration > Hosts.  Click Import.
7. Click the 'Choose File' button and select the file downloaded in step 1.
8. Click the 'Import' button

![Import Image](https://github.com/jack-valko/Zabbix-Meraki-Discovery/raw/main/images/zabbix-54-host-import.jpeg)

If any errors appear, congratulations!  You have an install I have not tested.  [Please file a bug.](https://github.com/jack-valko/Zabbix-Meraki-Discovery/issues/new)

## Configuration
1. In the Zabbix UI, navigate to Configuration > Hosts
2. Click on host 'Meraki Cloud'
3. Click on 'Macros'
4. Set a new value for {$APIKEY}, enter your Meraki API Key in the text field
5. Click the 'Update' button

## Running Your First Discovery
Discovery is designed not to burden the Meraki API with a lot of traffic, so it is set at a period of 12h.  You don't have to be this patient, run your first discovery by hand.
1. In the Zabbix UI, navigate to Configuration > Hosts
2. Click on host 'Meraki Cloud'
3. Click on 'Discovery rules'
4. Click on 'Meraki Device Discovery'
5. Click the 'Execute now' button at the bottom of the page

Running by hand will take a few minutes to complete and about an hour for all SNMP data to start trickling in.

## Monitoring Meraki Devices with Interfaces SNMP
The templates uses SNMP to monitor individual Meraki devices that support SNMP, it does not use the Meraki Cloud SNMP. You should keep in mind:
1. Your {$SNMP_COMMUNITY} macro must match the SNMP string you entered into your Meraki Dashboard in [Network-wide > General > Reporting](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/SNMP_Overview_and_Configuration#Configuration)
2. Not all Meraki devices support SNMP, the template attempts to take this into account
3. The network between your Meraki devices and your Zabbix server or proxy will need to allow UDP 161 (snmp)
4. Your meraki devices will need to have unique LAN ip addresses
