# Bertelsmann_CloudArchitect_U1

A study note for the Bartelsmann scholarship for Cloud Architect in Udacity NanoDegree.

---
## What is DSC? ('Desired State Configuration)

I did not understand the purpose of Lesson 5-9 to 5-12 on 'Desired
State Configuration', DSC. I went through documents, and here is where
I ended up, what is DSC. I still do not fully understand DSC, and I
filled those gaps with my imagination. I marked such uncertain parts
with "(*)". So, please be cautious. I wrote the note by taking a VM as
a representative Azure-resource, so that I could have a concrete
idea. In reality it could be other resources, like storage or network.

The purpose of Desired State Configuration is the Configuration
Management. Configuration management originally comes from the
hardware development -- a development of a large, complicated
hardware, like an airplane. The goal of a configuration management is
to make a full list (=record) of all the parts that are used in the
airplane at each moment/phase of the development. With these lists (we
have many lists as a function of time), engineers can understand what
to buy, when some parts are broken. They can also tell what their
airplane can do, and cannot do, from the list.

In the case of Azure, the configuration management is 'Software
Configuration Management'. Similar to a hardware configuration
management, engineers have to understand what their system (computer)
is made of. In order to understand what their system can do, the
engineers need to know what applications, library, python versions,
etc. are installed in their machine.

However, there are so many applications and libraries in a computer,
with entangled dependencies. It is almost impossible to keep the
software inventory manually by human. Therefore software configuration
management is usually automated. DSC is used for that purpose. DSC
installs a software again when it is accidentally removed. DSC deletes
a software when it is accidentally installed. I believe DSC can
update/retain a software to a specific version (*). DSC is in a sense
similar to git that enables the source code evolution, but prevents
bugs get into codes. In the case of DSC, it is not source codes, but a
suite of software installed in a computer.

DSC is a function of PowerShell, and is typically executed from
PowerShell (*). The script we used (='iis.ps1' with 'Configuration
....) was a PowerShell script.

In order to automatically install/uninstall a software in a computer
without human intervention, DSC requires Azure Automation. Azure
Automation has a function, for instance, to start and stop a VM
automatically (at a certain time, or with a certain trigger). This is
a separate service from Azure Portal. One can automate start/stop
computers on Azure, as well as on-premise (*) by running command from
your local (=your laptop) PowerShell (*). This is why we need to
create Automation Account, separately from MicroSoft account that we
use to log-in Azure Portal.

### Glossary

*WMF* means 'Windows Management Framework', and is a software package
that contains *WinRM* and *PowerShell*. WinRM means Windows Remote
Management, and is a Web Service Management of the Microsoft version
(*). *Web Service Management* is a type of protocol (=a bundle of
rules, like HTTP, SSH, etc.) to communicate to web servers/services
(*). Therefore we need WMF, more specifically, WinRM and PowerShell
inside WMF

The differences among similar-looking services:

*Azure Policy* : it sets individual restriction when we create a VM
(location, size, ...). The restrictions are mostly on hardware (*).

*ARM Template* : by running a given description (=script) on the command
line, one can create a VM without clicking menus on Azure Portal. The
restrictions are mostly on hardware (*).

*Azure DSC* : it also sets restrictions when a VM is created, like Azure
Policy, but the DSC restrictions are exclusively on software (*). The
business of Azure Policy and ARM Template are to create (=deploy)
VMs. The business of DSC, however, includes monitoring (=watch) the
status of the software combination. DSC intervenes (install/remove
software) when unwanted changes are made in the combination of the
software.

*Azure Blueprint* : Use all of them above (hardware+software) from one
 place (*).



---
## Virtual Network
IP address must be unique in a subscription
Virtual Private Network (VPN)
= one virtual network that consits of Azure + On-premise)
IP address must be unique inside a VPN)

(scenario)
ExpressRoute: No site-to-site. No encription. p2p. a2a. CloudExchange

network security group: one for one subnet
service endpoint: SQL/storage only from subnet with an endpoint
                  - access from internet closed

NVA = firewall

*.*.*.0: network adress
*.*.*.1: taken by azure for default gateway 
*.*.*.2,3: taken by Azure DNS
*.*.*.256: broadcast

VNet: need one subnet inside. max 50 in subscription.
      can be increased to 500

pvivate ID : to comunicate to other (Azure) resources in teh VNet
             - do not change 

public ID: to communicate to internet
           - not change, if dynamic, and rebooted or stopped
           - change, if dynamic, and deallocate


need static public ID
- static IP in DNS name resolution
- static IP in security restriction
- static IP linked in TLS/SSL cetificates
- static IP in  Firewal rule
- static IP in Domain Controllers/DNS server

Basic / Standard : cannot change afterward

NSG : 5 tuple, multiplication, use 'Effective security rules'
use inside VNet
- default
65000 AllowVnetInBound
65001 AllowAzureLoadBalancerInBound
65500 DenyAllInBound

65000 AllowVnetOutBound
65001 AllowInternetOutBound
65500 DenyAllOutBound

service tag: change name to range of IP addresses
VirtualNetwork/Internet/SQL/Storage/AzureLoadBalancer/AzureTrafficManager

---
Azure Monitor : Performance
Azure Security Center : Security/Compliznce, summary
   can install agent to Iaas/Paas 
Azure Sentinel : Network Security in more detail
   automated action. playbook/notebook, enterprise
   Log Analytics, workspace, connectors

Application Insights:
  instrumentation = install agent

---
Firewall
unrestricted scalability



---
## Virtual Machine
- standard disk = HDD. Blobk, Page, 
- premium disk = SDD. Page Blob only

Blob Storage (hot and cool. no archive). block blob, incremental blob

OS Storage : system disk. C:. <4GB. image. Linux 30GB, Windows 127GB
Temporary Storage : swap. D: 
Data Storage : all others. persistent. < 32TB
(All blob)

senario : onpremise -> azure
VHD (Virtual Hard Drive). Stored as page blob
1. local disk -> VHD ('Add-AzVhd') -> Storage Account 
2. -> connect to VM

add disk to Linux VM :
az vm disk attach \
  --vm-name support-web-vm01 \
  --name uploadDataDisk1 \
  --size-gb 64 \
  --sku Premium_LRS \
  --new

(how to get public ID)
az vm show


get ipaddress

(to execute command lsblk in 'vm01')
ssh azureuser@ipaddress lsblk


---
99.999% five nine

Premium Storage
- Dynamic CRM, Exchange Server, SAP Business Suite,
- SQL Server, Oracle, Share Point
- LRS only

Ultra Disk - can chanbe performance, no need to restart VM
SAP HANA
Dv3  : cannot use Premium
Dsv3 : can use Premium

Standard SSD: small size VM, but fast storage case

LRS : 3 in 3 VMs   in a data center
ZRS : 3 in 3 Zones in a region 
GRS : 2 in 2 Regions
GZRS : (3 in 3 Zones) x 2 in 2 Regions

RA-GRS  : second one is read-only. only for backup 
RA-GZRS : second one is read-only. only for backup 

disk 'level'
P4    -  P80
32GB  -  32TB

---
(Making disk size larger) cannot make it smaller
- stop VM
az vm deallocate --resource-group --name
az disk update  --resource-group --name --size-gb 200
az vm start --resource-group --name
expand particition 'diskpart' / parted / resize2fs

az disk list \
  --query '[*].{Name:name,Gb:diskSizeGb,Tier:sku.tier}' \
  --output table




---

az configure --defaults location=eastus
az configure --defaults group="learn-02e70cf3-d998-4177-8d30-1e12a9040db6"
az vm create \
  --name support-web-vm01 \
  --image Canonical:UbuntuServer:16.04-LTS:latest \
  --size Standard_DS1_v2 \
  --admin-username azureuser \
  --generate-ssh-keys
az vm disk attach \
  --vm-name support-web-vm01 \
  --name uploadDataDisk1 \
  --size-gb 64 \
  --sku Premium_LRS \
  --new  

ipaddress=$(az vm show \
  --name support-web-vm01 \
  --show-details \
  --query [publicIps] \
  --output tsv)

ssh azureuser@$ipaddress lsblk

az vm extension set \
  --vm-name support-web-vm01 \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-add-and-size-disks-in-azure-virtual-machines/master/add-data-disk.sh"]}' \
  --protected-settings '{"commandToExecute": "./add-data-disk.sh"}'

---
## Topics to note
- NanoDegree Lab System
- Load balancer
- Virtual Network
- Virtual Private Network and VPN Gateway (+ Express Route)
- Application Gateway
- VM and App Server
- Network Security Group / Application Security Group