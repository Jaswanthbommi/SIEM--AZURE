# SIEM—AZURE
![1](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/0a493995-570d-42a5-aa5e-6f2a2bc751b5)
## Getting Started with SIEM: A Comprehensive Azure Sentinel Tutorial - Real-Time Cyber Attack Mapping and Response Strategies for Beginners!

Embarking on our cybersecurity journey, let's demystify SIEM. If you're new or seeking a deeper understanding, you're in the right place. <br>
__SIEM (Security Information and Event Management)__ is our digital guardian, collecting and analyzing data across IT systems. It correlates events, triggers alerts for potential threats, and aids in swift incident response. <br>

![2](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/c825137d-4aab-4a85-8d11-edd5f5dfee95)

In simpler terms, think of SIEM as a digital detective that sifts through a multitude of logs, events, and activities across your network, applications, and systems. Its primary mission? To identify and neutralize potential security threats before they can wreak havoc. <br>
#### __Objectives :__
-	Establishing and deploying diverse Azure elements such as Virtual Machines (VMs), Log Analytics Workspaces, and Azure Sentinel. <br>
-	Proficiency and familiarity with Microsoft Azure Sentinel, a Log Management Tool operating within the SIEM (Security Information and Event Management) framework. <br>
-	Engaging with third-party API Calls. <br>
-	Applying KQL for log querying purposes. <br>
-	Acquiring skills in interpreting Windows Security Event Logs. <br>
-	Leveraging Workbooks, specifically utilizing a World Map to create an interactive display of attack statistics. <br>
__Technologies and prerequisites:__
-	Microsoft Azure subscription. <br>
-	Utilization of Azure Services, including Sentinel, Log Analytics Workspace, Workbooks, and Network Security Groups. <br>
-	Powershell. <br>
-	Access to Remote Desktop Protocol (RDP). <br>
-	Integration of a third-party API, specifically [ipgeolocation.io]( https://ipgeolocation.io) <br>
-	Implementation of a customized [Powershell Script](https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1) developed by Josh Madakor. <br>
### __Overview :__
![3](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/3862bb53-18a7-4088-8a1d-88c12bfdf5ad)

### This image illustrates the essence of the project.

## __Step 1:__ Create a Microsoft Azure Account: [Azure]( https://azure.microsoft.com/en-us/free/)

<img width="1116" alt="4" src="https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/7e70ea66-5875-4e29-bcae-23d4763d1013">

## __Step 2 :__ Configure the virtual machine for the honeypot.

![5](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/57bf9a85-96e9-49c1-8d6c-7be5d26a21e2)

Once you've completed the sign-up process, the Azure portal home page will be displayed. Navigate to the top and click on the search bar, then search for "virtual machines." This particular machine will serve as our honeypot, positioned on the internet to attract potential attackers. <br>

The objective is to intentionally make the VM highly discoverable, enticing individuals to identify its online presence and initiate attacks. While this approach contradicts standard security practices, it is crucial for our lab's purpose, providing valuable hands-on experience with SIEM tools. <br> 
#### __Project Details__
-	Create a new resource group and give it a name (honeypot-lab) <br>
### A resource group is a container that helps organize and manage related cloud resources.
__Instance Details__
-	Give your virtual machine a name (honeypot-vm) <br>
-	Choose a recommended region: ((US) West 3) <br>
-	Availability options: No infrastructure redundancy required <br>
-	Security type: Standard <br>
-	Image: Windows 10 Pro, version 22H2 - x64 Gen2 <br>
-	VM Architecture: x64 <br>
-	Size: Default is fine (Standard_D2s_v3 – 2vcpus, 8 GiB memory) <br>
__Administrator account__
-	Set up a username and password for the virtual machine. <br>
### IMPORTANT: these identification details will be used to log into the virtual machine. (Make sure to keep them in mind)
__Inbound port rules__
-	Public inbound ports: Allow RDP (3389) <br>
__Licensing__
-	Confirm licensing <br>
-	Select __Next : Disks >__ <br>
![6](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/ac40cc2d-8a84-45de-b6d2-2619b4520f9b)

__Disks__
-	Leave all defaults <br>
-	Select Next : Networking > <br>
__Networking__
__Network interface__
-	NIC network security group: Advanced > Create new <br>
### A network security group contains security rules that allow or deny inbound network traffic to, or outbound network traffic from, the virtual machine. In other words, security rules management. 
-	Remove Inbound rules (1000: default-allow-rdp) by clicking three dots <br>
-	Add an inbound rule <br>
-	Destination port ranges: * (wildcard for anything) <br>
-	Protocol: Any <br>
-	Action: Allow <br>
-	Priority: 100 (low) <br>
-	Name: Anything (ALLOW_ALL_INBOUND) <br>
-	Select __Review + create__ <br>

![7](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/d21606ce-ba61-4df5-ae8b-5b2cd8e0ff4a)

### Configuring the firewall to allow traffic from anywhere will make the VM easily discoverable.

## __Step 3:__ Establishing a Log Analytics Workspace
-	Search for "Log analytics workspaces" <br>
-	Select __Create Log Analytics workspace__ <br>
-	Place it in the identical resource group as the VM (honeypot-lab) <br>
-	Give it the name you choose (honeypot-law) <br>
-	Add to the same region (West US 3) <br>
-	Select __Review + Create__ <br>
![8](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/04f46d2d-6a1c-43b7-8331-e52ee050149a)

### The Windows Event Viewer logs will be ingested into Log Analytics workspaces in addition to custom logs with geographic data to map attacker locations.
## __Step 4:__ Configure Microsoft Defender for Cloud
-	Search for "Microsoft Defender for Cloud" <br>
-	Under __Management__ click on "Environment settings" -> Subscription Name -> Log Analytics Workspace Name (honeypot-law) <br>
![9](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/9b0f4da6-37fd-4bec-9c88-4e8d645ac225)

__Settings | Defender plans__
-	Foundational CSPM (Cloud Security Posture Management): ON <br>
-	Servers: ON <br>
-	SQL servers on machines: OFF <br>
-	Click __Save__ <br>
![10](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/17506501-2d20-4155-9fde-ad903cc15a7d)
__Settings | Data collection__
-	Select "All Events" <br>
-	Click __Save__ <br>
## __Step 5:__ Associate the Virtual Machine with the Log Analytics Workspace
-	Look for "Log Analytics workspaces" <br>
-	Select workspace name (honeypot-law) -> "Virtual machines" -> virtual machine name (honeypot-vm) <br>
-	Hit __Connect__ <br>
<img width="744" alt="11" src="https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/999ca46d-c970-448d-90bc-9430094a8bd4">

## __Step 6:__ Configure Microsoft Sentinel 
-	Look for "Microsoft Sentinel" <br>
-	Hit __Create Microsoft Sentinel__ <br>
-	Choose Log Analytics Workspace name (honeypot-law) <br>
-	Hit __Add__ <br>
![12](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/1f32519b-0b9a-489b-8801-f5475b62a43f)
## __Step 7:__ Deactivate the Firewall on the Virtual Machine
-	Go to Virtual Machines and find the honeypot VM (honeypot-vm) <br>
-	By clicking on the VM copy the IP address <br>
-	Log into the VM via Remote Desktop Protocol (RDP) with credentials from step 2
-	Accept Certificate warning <br>
-	Select NO for all __Choose privacy settings for your device__  <br>
-	Click __Start__ and search for "wf.msc" (Windows Defender Firewall) <br>
-	Click "Windows Defender Firewall Properties" <br>
-	Turn Firewall State OFF for __Domain Profile Private Profile__ and __Public Profile__ <br>
-	Hit __Apply__ and __Ok__ <br>
-	Ping VM via Host's command line to make sure it is reachable ping -t  VM IP <br>

<img width="1116" alt="13" src="https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/9c93652c-faf2-452f-95ba-afe10d1307d2">

## __Step 8:__ Develop a Script for the Security Log Exporter
-	In VM open Powershell ISE  <br>
-	Set up Edge without signing in <br>
-	Copy [Powershell script](https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1) into VM's Powershell (Written by Josh Madakor) <br>
-	Select New Script in Powershell ISE and paste script <br>
-	Save to Desktop and give it a name (Log_Exporter) <br>

![14](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/a2d1f884-472d-4e52-b688-626941858cdc)

-	Make an account with [Free IP Geolocation API and Accurate IP Lookup Database]( https://ipgeolocation.io) <br>

### This account is free for 1000 API calls per day. Paying 15.00$ will allow 150,000 API calls per month.

-	Copy API key once logged in and paste into script line 2: $API_KEY = "<API key>". <br>
-	Hit __Save__ <br>
-	Run the PowerShell ISE script (Green play button) in the virtual machine to continuously produce log data <br>
![15](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/98393323-c16d-4040-a366-b50feee9cd72)
### Data will be exported from Windows Event Viewer and imported into the IP Geolocation service by the script. The latitude and longitude will then be extracted, and a new log file called failed_rdp.log will be created in the location specified below: C:\ProgramData\failed_rdp.log

## __Step 9:__ Log Analytics Workspace - Creating a Custom Log
-	To add the extra information from the IP Geolocation service to Azure Sentinel, create a custom log <br>
-	Search "Run" in VM and type "C:\ProgramData" <br>
-	Open file named "failed_rdp" hit __CTRL + A__ to select all and __CTRL + C__ to copy selection <br>
-	On the host PC, open notepad and paste the information <br>
-	Save to desktop as "failed_rdp.log" __Note:__ make sure it's saved as a (.txt) text file. I had issues with formatting when saving in (.rtf) rich text format. <br>
-	In Azure go to Log Analytics Workspaces -> Log Analytics workspace name (honeypot-law) -> Custom logs -> __Add custom log__ <br>
__Sample__
-	Select Sample log saved to Desktop (failed_rdp.log) and hit __Next__ <br>
__Record delimiter__
-	Review sample logs in Record delimiter and hit __Next__ <br>
__Collection paths__
-	Type > Windows <br>
-	Path > "C:\ProgramData\failed_rdp.log" <br>
__Details__
-	Give the custom log a name and provide description (FAILED_RDP_WITH_GEO) and hit __Next__ <br>
-	Hit __Create__ <br>
![16](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/9b56b767-ce75-41fb-949e-af65b97d0bc4)

## __Step 10:__ Retrieve Data from the Custom Log through Querying
-	In Log Analytics Workspaces go to the created workspace (honeypot-log) > Logs <br>
-	Run a query to see the available data (FAILED_RDP_WITH_GEO_CL) <br>
### May take some time for Azure to sync VM and Log Analytics
 ### As of March 31st, 2023, Microsoft has disabled the creation of new custom fields and has migrated to KQL. You can learn more about it [here]( https://learn.microsoft.com/en-us/azure/azure-monitor/logs/custom-fields-migrate)

![17](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/471c6ec5-09fe-485d-8ced-727f0cc6cd9e)

### so there is a way to get all the fields sorted will be shown in the next step
## __Step 11:__ Generate a Global Attack Map in Microsoft Sentinel
-	Access Microsoft Sentinel to view the Overview page and available events <br>
-	Click on __Workbooks__ and __Add workbook__ then click __Edit__ <br>
-	Delete default widgets (three dots -> remove) <br>
-	Click __Add__->__Add query__ <br>
-	Now type the following code FAILED_RDP_WITH_GEO_CL #as in video extend CSVFields split(RawData, ) #this line use to split output after comma into seperate value with and create new column extend timestamp_CF todatetime(cSVFields[8]) #choose value 9th in extend label CF = tostring (CSVFields[7l) extend country_CF tostring(CSVFields[6|) 1 extend state CF = tostring(CSVFields[5|) lextend source CF tostring(CSVFields[4)) extend user CF = tostring(CsVFields[3]) extend dest CF tostring(CSVFields|2]) extend longitude CF tostring(CcSVFields[1)) lextend latitude CF tostring(CSVFields[o|) summarize event_count=count() by source CF, tostring (latitude CF), tostring (longitude CF), country CF, label CF, dest CF REMO <br>

### By using this method, we can bypass the need for field extraction.

-	When results appear, select __Map__ from the __Visualization__ drop-down box. <br>
-	Choose __Map Settings__ to make additional adjustments <br>

__Layout Settings__
-	Location info using: Latitude/Longitude <br>
-	Latitude: latitude <br>
-	Longitude: longitude <br>
-	Size by: event_count <br>
__Color Settings__
-	__Coloring Type:__ Heatmap <br>
-	__Color by:__ event_count <br>
-	__Aggregation for color:__ Sum of Values <br>
-	__Color palette:__ Green to Red <br>
__Metric Settings__
-	__Metric Label:__ label <br>
-	__Metric Value:__ event_count <br>
-	Click __Apply__ button and __Save and Close__ <br>
-	Save as "Failed RDP International Map" in the same region and under the resource group (honeypot-lab) <br>
-	Keep refreshing the map to show more inbound failed RDP attacks <br>
![18](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/29535378-deb7-4add-8e01-4e716a6d68af)

### The conclusive outcome of the map after an 8-hour duration.
![19](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/b11bef06-a3a7-4340-8944-f65becf44c74)

### Inspecting the Event Viewer for Event ID 4625
![20](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/965d70bc-d768-4e5a-b682-84542f2fe2c6)
 ### The log file alongside the shell displaying the output.
## __Step 13:__ De-provisioning Resources
### CRUCIAL: DON'T SKIP !
-	Look for "Resource groups" -> name of resource group <br>
-	Key in the name of the resource group (honeypot-lab) to verify removal of resources <br>
-	Select the __Apply force delete for selected Virtual machines and Virtual machine scale sets__ box <br>
-	Click __Delete__ <br>
![21](https://github.com/Jaswanthbommi/SIEM--AZURE/assets/62924828/8fa10b9c-c1bc-429f-8ecd-0d029f68814a)

### Uneliminated resources can consume free credits, and costs may begin to accumulate.
__Key Takeaways__
Following the completion of this lab, here are several insights to glean from the experience: <br>
1. __Global Exposure:__ The moment your devices connect to the internet, they become susceptible to individuals worldwide, with the potential for discovery and attempted access. <br>
2. __Password Strength:__ Emphasize the importance of robust password habits. The prevalence of failed login attempts in our logs underscores the vulnerability to brute force attacks, highlighting the necessity for strong passwords. <br>
3. __Security Measures:__ This lab illustrates how tools like Azure Sentinel can enhance cybersecurity efforts. Keeping abreast of the latest security technologies is crucial for reinforcing an organization's security posture and preserving its integrity. Cybersecurity analysts can leverage such tools to bolster the defense against evolving threats. Stay informed and proactive to maintain a robust security stance for your organization. <br>

In summary, this lab underscored the significance of implementing robust security controls and accurate configurations to safeguard your devices and the sensitive information they contain. Despite having security measures in position, the demonstration highlighted that attackers can still establish a connection and initiate brute force attacks on devices. It emphasizes the ongoing need for vigilance and proactive security measures to mitigate potential risks effectively. <br>
### Stay secure!
