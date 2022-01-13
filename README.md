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
## Topics to note
- NanoDegree Lab System
- Load balancer
- Virtual Network
- Virtual Private Network and VPN Gateway (+ Express Route)
- Application Gateway
- VM and App Server
- Network Security Group / Application Security Group