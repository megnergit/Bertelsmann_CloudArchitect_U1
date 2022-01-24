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
#### Azure Container Instances
Persistent Storage : need Azure Files Share 


---
#### Moving Resources
- another virtual network
- another resource gropu
- another subscription
- another region
Deleting resource group
+ check if other RG uses resource in
Remove-AzResourceGropu -Name "ContosoRG01"
Quota by _Subscription_


---
#### Azure API Management Service
Azure API Gateway : an instance of API Management Service

---
#### Web App
F1/D1: single instance (free/shared level)
B1:2 instance
S1/P1: autoscaling
App Service Plan
Standard <10 0.40/hr
Premium  <20 0.80/hr 99.95%
Isolated <100 1.60/hr 99.95%
Metric: Disk Queue, HTTP requests
Schedule
scale out rule 'or'
scale in rule 'and'
metric | scale to a specific instance count (schedule)


---
#### VM Scale Set
default : 2 instances, 1 loadbalancer
health probe
* az group create
* az vmss create
* az network lb probe create (ping)
* az network lb rule create 
low-priority scale set:automatic shutdown, 80% cheaper


---
#### Redis
transaction MULTI/EXEC/DISCARD
do not support rolllback?
DISCARD => stop
failed => mess
ServiceStack.Redis : C# library
IRedisClient.CreateTransaction()
QueueCommand()
Commit()


---
#### Azure Function
timeout 5 min, max 10 min.
cannot execute a function that takes > 10 min
=> Durable Functions
stateless. statefull -> use storage
HTTP, queue
max 200 instance?

payasyougo : intermittant, autoscaling
App Service: continuous (not severless)

---
#### Azure Event Hub
=> mostly for analysis or IoT | Azure Steam Analytics
AMQP : initial overhead high, first transmission
HTTPS : each overhead low
replace Kafka
Basic/[Standard]/Premium/Dedicated
1. Create a namespace: throughput/pricing/performance metric
az eventhubs namespace create
2. Create event hub in the namespace: partition/rentention
az eventhubs eventhub create

3. create storage account at subscriber
az stroage account create
az stroage account key list
az stroage account show-connection-string
az stroage container create

default partition: 4
max publication size : 1 MB

---
#### Azure Backup
SQL Database, point-in-time backup
Blob stored in RAGRS


---
#### Cosmos DB (~Cloud Spanner)
NoSQL, global
MongoDB/Cassandra/Gremlin, JSON,XML
Multimaster, 99.999%

---
#### Storage Account
hot/cool(30d)/archive(180d) need StorageV2+Blob storage
GRS: GR + LRS
RAGRS: GRS (one is read only. more expensive than GRS)
GZRS: GR + ZRS
need StorageV2


---
#### VNet Peering
- global, over subscritpions, over regions.
- no gateway (no cost for gateway)
- traffic cost
- when one connected, the other also connected

HTTP = TCP at 80
RDP  = TCP at 3389

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
behind gateway? in DMZ? 

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
Network:
- VPN Gateway
- Application Gateway
- Firewall
- Azure DNS
- Azure Bastion


---
Firewall : stateful, (multiple) static IP, SNAT/DNAT
unrestricted scalability, loggging, Azure Monitor
high-availability, Availability Zones
(no additional LB required. _FOR_ firewall)  
(LB for backend still required?)
Microsoft Threat Inteligence, list of malicious IP
FQDN filtering HTTP/S traffic on not IP, but on name
hub-spoke. subnet /27. Bastian, VPN Gateway
SecOps|DevOps separation, cross subscription
log -> Azure Monitor
1. NAT rules : Firewall external IP (single) -> private address
2. Network rules : TCP/UDP/ICMP(non HTTP/S)
3. Application rules : full name. outbound


---
Azure Monitor : Azure + on-premise, metric + log
insights/analysis/integrate/resopnd/visualize
insights: app, vm, container
analyzie: matric/log analytics
integrate: logic apps, export APIs
respond: alert/autoscale
visualize: dashboard/workbook/power BI
SQL: enable diagnostics
vm: add agent
kusto:  query language

----
Azure Monitor Log -> store in Log Analytics Workspace
VM syslog, windows event log
VM insights 



---
Azure Alert <- Azure Monitor
metric alert : CPU 95%
activity log alert : resource deleted
log alert : 404 error



---
Azure DNS

---
Azure Queue Storage 
REST base. store millions of messages.

Azure Service Bus
message broker 
Service Bus Topic : multiple subscription
Service Bus Queue : at-most-once, FIFO, transaction (=atomic), push
Queue Storage : need audit, >80GB
queues: temporary storage, FIFO, single receiver
topics: multiple subscription 
Service Bus Queue vs Storage Queue
256 KB | 64 KB
at-most-once/at-least-once | -
FIFO | FIFO (not guranteed)
can group | -
RBAC | -
80GB| unlimite queue size
 -  | log
need SAS keys
clients neeed
Namespace = endpoint bicycleService.servicebus.windows.net
access key 
Namespace + access key = connection string
await SendMessageAsync (can send while waiting)
      <


---
Scope
Policy : MG/SUB/RG
Role : Sub/RG/R


---
Azure Active Directory (Azure AD)
Security Principal : an 'object' (user, group, service) to which
                     a role is given. 

---
Storage Account
Data in transitit : Client-Side Encryption, HTTPS, SMB 3.0
Shared Access Signature : delegated access, time interval
Authorization: RBAC,Sshared Key, SAS, Anonymous access (web contents)

---
Resource Lock
- Delete/ReadOnly
- can move anywhere, unless it is not deleted
+ storage may not be read even with ReadOnly
  + cannot see list of keys (need POST)
  + MS learn : 6-use-resource-locks-to-protect-resources
- lock is inherited

Azure SQL Database is a PaaS. 


---
END
===
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