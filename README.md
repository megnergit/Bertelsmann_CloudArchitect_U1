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

==========
# AZ-104
==========
----
#### Private Endpoint -> Private Link
PaaS

----
#### Service Endpoint
Endpoint of VNet (private IP) -> Azure service
now one can use Azure service from VNet
keep Azure serivice out of reach from public IP
service traffic does not need to go to UDR
1. Create Service Endpoint
   + which service?
   - Storage
   - SQL Database/Warehouse/PostgreSQL/MySQL/CosmosDB
   - Key Vault
   - Service Bus/Event Hubs
----
#### User Defined Route Table
System Route : default route inside a VNet, between Subnets
User Defined Route: define NVA as a next hop
1 route table to many subnets
one subnet can have only one route table
NVA are VMs
1. Create Routing Table
- Name, Subscription, RG, location, 
- VNet route propagation
  + current subnet route is automatically added to table
2. Create Custom Route ('Add route')
- Name
- Address prefix (= subnet mask) 10.0.1.0/24
- Next hop : [VNet Gateway, NVA, VNet, Internet...]
  + let's say NVA is 10.0.2.4
=> any incoming packets that are directed to 10.0.1.0/24
   are sent to 10.0.2.4 first.
3. Associate Route Table
- 'Add subnet. 10.0.1.0/24, Route Table Name
----
#### ExpressRoute
collocation environment/center ~ community cloud
Exchange provider facility
MPLS multiprotocol label switching
100Gbps
- regular data migration
- disaster recovery (for on-premise)
layer 3, BGP
Redundancy: ExpressRoute has two links to MSEE
MSEE: Microsoft Enterprise Edge Routers
Azure/Microsoft 365/Dynamics 365
All regions in one Geo
ExpressRoute to Amsterdam -> North+West Europe
ExpressRoute Premium -> Global except domestic
ExpressRoute Global Reach -> connect multiple on-premises
site-to-site = ExpressRoute + VPN Gateway link
need 2 Gateways - VPN Gateway + ExpressRoute Gateway
ExpressRoute - 3 way conection
1 CloudExhante (colocate?) layer 2/3
2 Point-to-point. layer 2/managed layer 3
3 Any-to-any (IPVPN). many VNets to many on-premise. WAN. MPLS. layer 3
4 NO site-to-site. but can do that in PowerShell

----
#### Azure Virtual WAN
many on-premise + many VNets
on-premise -> (regional Hub) -> Azure VNet
(resional Hub 1) = (regional Hub 2) by Azure backbone
site-site and point-to-site by VPN Gateway + ExpressRoute
Basic : site-site by VPN Gateway only
Standard : ER-p2s. 

----
#### VPN Gateway
IPSec: encription IKES: authentication, 
minimun 2 VMs, can be in Availability Zones
1. VNet, Subnet, DNS server (internal)
2. AzureGatewaySubnet
3. Create VPN Gateway
4. Create Local Network Gateway. This is on Azure
5. VPN Device (on-premise)
6. VPN Connection
No overlap in IP Address space
GatewaySubnet /27 or /28. No other VM on GatewaySubnet
(subnet required for Bastion. no for Load Balancer)
VPN Gateway
- Name, Region, VPN/ExpressRoute, Route/Policy-base
- SKU:VpnGW1 (number of tunnels), Generation
- active-active, BGP ASN
Route-Base: point-site/inter-VNet/multiple-site-site/ExpressRoute
Use IP forwarding, Route table to direct packet to tunnel. any-to-any
Policy base cannot do point-site. only IKEv1
combination of address prefixes between VNet and on-premise
need an access list. SKU: Basic only. one tunnel. site-to-site only
SKU: VPNGw1-5, 650Mbps-10GBps
Local Network Gateway is in on-premise, but need to specify on Azure
IP Address of VPN device. Must be public IP
Address space (aka Address prefix aka IP address mask) of on-pre machines.
VPN Device (on on-pre) Cisco, Juniper, Ubiquiti, Barracuda
need Shared Key (PSK? one can create yourself),
Public IP. VPN device configuration script
Add connection => Portal: when two VNets are on same subscription
active-standby 10-15s, 60-90s


----
#### VNet Peering
- Region VNet Peering
- Global VNet Peering (but not from Azure Goverment)
For Azure Goverment, regional peering only
- Microsoft Backbone Network. No encryption. No Gateway
- No downtime
one Gateway for one VNet
Allow Gateway Transit
nontransitive
User Defined Route : eneable next hop (IP address)
Service Chaining
Status : Initiated Connected

----
#### ARM Tempalte
Get-AzSubscription
$context = Get-AzSubscription -SubscriptionId {Your subscription ID}
Set-AzContext $context
Set-AzDefault -ResourceGroupName learn-09e42320-c4cd-434a-87a2-dcfa7246a4f2


----
#### Azure CLI
az vm restart -g RG -n VM1
Azure Cloud Shell <- Azure Portal |_>
- Linux: apt-get(Ubuntu), yum(Redhat), zypper(SUSE)
- Mac: brew
CLI: variable=variable
PowerShell: $variable=variable
brew update
brew install azure-cli
az (group) (subgroup)
az storage (account|blob|queue)
to find command : az find blob (AI robot)
az find "az vm"
az find "az vm create"
az storage blob --help
az storage blob -h
az login => sign-in page => to connect to a subscription 
az group create -n NAME --location LOCATION (to put metadata)
"Wset US" "West Europe" "westus" "westeurope"
az group list -o table



----
#### Virtual Machine Extention
- Extensions
- Custom Script
- Desired State Configuration (DSC)

- Extensions
small aplications. post-deployment configuration.
automation task. software installation. anti-virus protection.
like an autoexec.bat.
- Custom Script Extensions (CSE)
Azure Portal -> Extensions -> PowerShell script
Set-AzVmCustomScriptExtentions -FileUri https://scriptstore.blob..
Timeout: 90 min; Dependencies;
- Desired State Configuration
DSC is a PowerShell Script *.ps1
Configuration IISInstall{
 Node "localhost"{
  WindowsFeature IIS{ # resource block
   Ensure = "Present"
   Name = 'Web-Server"
}}}

----
#### Virtual Machine Scale Set
Unplanned Hardware Maintenance: live migration, low performance
Unexpected Downtime: automatic heal, reboot
Planned Mainenance: no impact
availability set: VM should habe managed disks. 99.95%
fault domain  
update domain <= 20 
availability zones: 99.99%
single VM + Premium disks: 99.9% 
Azure Load Balancer: layer 4 (transport, TCP, UDP)
Azure Application Gateway: layer 7 (application, HTTP, X500)
                           SSL termination
scale set < 1000
scale set with custom image < 600
Azure Spot Instance: 

----
#### Virtual Machine
1. network
2. Region
3. Size
4. Storage/OS
       compute: webserver, application server, network appliance
memory: SQL
OS system disk : SATA
data disk : SCSI
Temp. disk : /dev/sdb /mnt
UbuntuLTS (longterm support)
monitor, agent, script, extention
window:rdp
linux:ssh PuTTY
Azure Bastion:PaaS RDP or SSH
WinRM (Windows Remote Management): command-line session
non-interactive PowerShell scripts. port 5986 (NSG!)
1. Create Key Bault
2. Create self-signed certificate
3. Upload self-signed certificate to Key vault
4. Identify URL of certificate
5. Refer URL in VM configuration
Linux VM : SSH = encripted
public Key => VM
private Key => your laptop
SSH-RSA 2048bit

==========
# 30 days
==========
#### Azure PowerShell
Ubuntu Devian apt-get
RedHat CentOS yum
SUSE zypper
Fedora dnf
Mac Homebrew
brew install --cask powershell
pwsh
commandlet cmdlet
Verb-Object
Get-Module (like a library)
Az : Azure PowerShell cmdlet official name, ~hundreds cmd
PowerShell Gellery
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
Update-Module -Name Az
(connect so Azure)

Connect-AzAccount
Set-AzContext -Subscription ''
Get-AzResourceGroup
Get-AzResourceGropu | Format-Table
New-AzResourceGropu -Name NAME -Location LOCATION
Get-AzResource | Format-Table
Get-AzResource -ResourceGropuName ExerciseResource
New-AzVm -ResourceGropuName $RG -Name $VM1 -Credential $OBJ
         -Location $LOCATION -Image $IMAGE
Remove-AzVM
Start-AzVM
Stop-AzVM
Restart-AzVM
Update-AzVM
Get-AzVM -Status

$ResourceGroupName = "ExerciseResources"
$vm = Get-AzVM  -Name MyVM -ResourceGroupName $ResourceGroupName
$vm.HardwareProfile.vmSize = "Standard_DS3_v2"
Update-AzVM -ResourceGroupName $ResourceGroupName  -VM $vm
(update the variable 'vm')

@vm | Get-AzPulicIpAddress
Integrated Scripting Environment
.ps1
variables: $loc = "East US"
$adminCredential = Get-Credential
New-AzResourceGroup -Name "RG" -Location $loc
loops: For ($i=1; $i-lt 3; $i++)
{
$i
}
script.ps1 -size 5 -location "East US"
param([int]$size, [sgting]$location)
UbuntuLTS (longterm support)


---
#### ARM Template
{
"$schema"
"contentVersion":"",
"parameters":{},     # type, defaultValue, allowedValues
"variables":{},
"functions":[],
"resources":[],
"outputs":{}
}
quick start template
if identical ARMT executed twice, nothing happens

---
#### Azure Kubernetes Service
not PaaS. only partly.


---
#### Azure Container Registry
az group create -n RG --location westeurope
az acr create -n NAME_UNIQ -sku Premium
- edit Dockerfile
az acr build --registry NAME_UNIQ --image hello:v1 .
az acr build repository list -n NQME_UNIQ 
credential: Azure AD (RBAC) | admin for registry
az acr update -n NAME_UNIQ --admin-enabled true
az acr credential show -n NAME_UNIQ => username, password
az acr replication create
az acr replication list
can automate build and deploymen
security, credential, encryption > Docker Hub
webhook
az acr task create
1. GitHub monitored by ACR (task)
   -> ACR build an image -> store
2. webhook by ACR (image updated)
   -> subscribed by App and Service -> App pull -> App restart


---
#### Azure Container Instances
Persistent Storage : need Azure Files Share 
az container create -g RG -n mycontainer --image mcr.microsoft.com/
DNS name label : can create new, but must be unique $RANDOM
az container show --query "{FQDN:ipAddress.fqdn}"
az container show --query "{ProvisioningState:provisioningState}"
http://aci-demo-24331.eastuslazurecontainer.io
restart policy : Always|Never|OnFailure 
--environment-variables COSMOS_DB_ENDPOINT=$COSMOS_DB_ENDPOINT
--source-environment-variables COSMOS_DB_ENDPOINT=$COSMOS_DB_ENDPOINT
stateless service
file share
az storage account create
az storage account show-connection-string
az storage share create -n NAME
az storage account keys list
az storage file list -s aci-share-demo -o table
az storage file download -s aci-share-demo -p FILENAME
az container logs
az container attach
az container exec
az monitor metrics list --resource CONTAINER_ID --metrics CPUUsage


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
#### Azure App Service
PaaS
deploy slot
CI/CD
Azure DevOps: build, release
create web app resource
windows -> monitor on -> Application Insights
az webapp up (automatic creation of Web App)
az webapp deployment source config-zip


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
	<

---
#### Storage for Virtual Machine
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