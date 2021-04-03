# Proxmox VE or VMware vShpere: Which is best?
In the space of containerisation and virtualisation, it may not be obvious to identify the software tool of trade. However,
there are two standout packages that most businesses opt for; Proxmox VE and VMware vShpere.

## What is Proxmox?
Proxmox, developed by Proxmox Server Solutions in Austria, is a complete open source server management platform for enterprise virtualisation released under the GNU General Public License.

It is a Debian-based Linux distribution with a modified Ubuntu LTS kernel enabling the deployment and management of VMs and containers, such as KVM (Kernel-based VM) for VMs and Linux Containers (LXC) for containers.

The software also includes a bare-metal installer, web-based management interface and many command-line tools. There is also a REST API to support third-party tools.

Proxmox can be clustered across multiple server nodes for high availability. When deployed, the resource manager called Proxmox VE HA Manager monitors all VMs and containers on the whole cluster and automatically gets into action if one of them fails.

There is also an integrated live/online migration feature, this enables the movement of VMs from one Proxmox VE cluster node to another without any downtime. The process can be initiated by administrators with either scripts or the web interface.

The Proxmox Virtual Environment supports a maximum of 12TB of RAM and 768 logical CPUs per host. It also supports Intel EMT64 or AMD64 with Intel VT/AMD-V CPU flag.

It also features a built-in firewall that is customisable allowing configurations via GUI or CLI. Firewall rules can be set up for all hosts inside a cluster or define rules for VMs and containers only.

## What is VMware vSphere?

vSphere is the most popular virtualisation software in VMware's portfolio and is now in its seventh iteration. It was originally launched in 2009 as 'VMware Infrastructure', but has changed substantially over the last 12 years.

The product includes hypervisor software and a management platform. The Type 1 hypervisor, also known as ESXi, is a bare-metal version that includes the OS kernel. It has vCentre server management system, previously known as VirtualCenter, that offers a centralised view across all ESXi hosts.

The hypervisor is similar to your average operating system in that it is directly installed into the physical hardware. This way, customers can create multiple virtual machines (VMs) and run systems like Windows, Linux, macOS, Solaris, and more on a single device. This means there is a layer of storage handled by virtualisation. 

Another bonus of ESXi is it can run on Intel processors (Xeon and up) and AMD Opteron and Epyc processors -- this crosses both 32-bit and 64-bit guest operating systems, although 32-bit processors aren’t supported. ESXi uses a 64-bit VMkernel.

The hypervisor, which can be installed on a hard disk, USB device, or SD card, can support the following resources per host: 4,096 virtual processors, 512 VMs, 4TB of RAM and 320 logical CPUs.

VMware’s ESXi is available as a free download or as part of a paid package. Naturally, the free version provides only limited functionality and can’t be managed by Center (see below). vSphere is currently on its 7th iteration, first announced in March 2020, and is the first version to feature vSphere with Kubernetes, formerly known as Project Pacific. 

VMware vCenter is a software suite that manages the whole of the VMware virtualisation infrastructure, acting as a single window. From here, the assignment of VMs to hosts is managed, as well as the assignment of resources to tasks, based on policies set by the administrator. A single instance of vCenter can manage up to 1,000 hosts at a time, across up to 10,000 active VMs or 15,000 registered VMs

It also enables the use of features such as vSphere Distributed Resource Scheduler (DRS), vSphere High Availability (HA), vSphere vMotion, and vSphere Storage vMotion. It also provides the API for vSphere and manages ESXi.

It can be installed on a supported version of Windows or used as a preconfigured Linux version known as vCenter Server Appliance. vCenter Server also permits Host Profiles, allowing users to define rules for specific ESXi hosts.

With the latest version of its virtualisation software - vSphere 7 - VMware has added full integration with Kubernetes, which it's touting as the "biggest vSphere innovation since the launch of the ESXi hypervisor". This means administrators can provision, run, and manage Kubernetes clusters on top of vSphere via the Kubernetes interface. Supporting both containers and VMs on a single platform allows vSphere 7 to run Kubernetes pods on VMs using the vSphere POD Service. VMware vSphere PODs can be managed like existing VMs.

## VMware VSphere vs Proxmox VE
ESXi is a mostly closed off, proprietary product that has a free version with limited features. However, enterprise features are not available in the free version.

Proxmox is a free, open source product based on other free, open source products (KVM, LXC, etc) with all features enabled.

VMware vSphere has more features overall than Proxmox, although Proxmox's features are more useful. Proxmox can automatically enable nodes to use the same shared storage when the user adds them to a cluster. While ESXi obliges the user to manually configure a node to use the shared storage from its cluster.

While both technologies are used for cloud computing and server consolidation, the typical usage profile of Proxmox is in virtualised server isolation and software development. VMware vSphere is more likely to be used for business-critical applications and infrastructure as a service (IaaS).

ESXi also uses proprietary technology to support virtualisation (VT-x for Intel processors and AMD-V for AMD processors). Compare this with the situation with Proxmox; its KVM uses generic x86 virtualisation technology.