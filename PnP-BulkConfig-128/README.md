# PnP-BulkConfig
Bulk upload of intent for onboarding network devices.

## Changes with 1.2.8
In 1.2.8, there is a change where the PnP workflow is integrated into the provisioning process.
This means a device can be onboarded into a site.

There is a new API to do this, and in addition, you no longer require credentials in the day0 configuration file

The other implication is that stack renumbering is part of the payload for the API call,
rather than encapsulated in the workflow.

The scripts in this directory take a CSV file of device serial number plus a siteName and a (day-0) template to be applied to them.
A PnP rule is created for each device, linking the serialnumber of the device to a site and the onboarding template in DNAC

When the device is connected to the network, it will discover DNA-C and begin the onboading process.

DNA-C will recognise the serial number of the device, and assign the device to a site, and deploy the Day0 template.

A simple example could include an initial configuration template, which contains variables.  

The script will recognise template variables and fill them in from the CSV file.

NOTE: Make sure the template is not empty.

## before running the scripts
In Cisco DNA-C go to the template editor and add an onboarding configuration template.  In the onboarding configuration template add the following template with the hostname and loopback address variables.  Save the template and then ensure you commit the template.

hostname $hostname

interface loopback0
  ip address $loopback 255.255.255.255
  no shut

Next you will want to create a network profile.  This is a major part of the integration of the day 0 process.  Next you add your targeted site to site to the network profile.

## work_files
Contains the inventory files.  These are CSV files with the device name, serial number, pid, Location, workflow and any variables required for the workflow template

## running the scripts
./10_add_and_claim.py work_files/test.csv

creates a pnp device rule and then claims it with a workflow and template variables.

you need to define the workflow and the template used.

./12_delete.py <serialnumber>
shows the rendered configuration for the device with serial number <serialnumber>.

./12_delete.py work_files/test.csv
cleans up afterwards. deleting pnp rules for all devices in the csv file
