---
title: Azure Site Recovery support matrix for disaster recovery of Azure IaaS VMs between Azure regions with Azure Site Recovery | Microsoft Docs
description: Summarizes the supported operating systems and configurations for Azure Site Recovery replication of Azure virtual machines (VMs) from one region to another for disaster recovery (DR) needs.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/21/2019
ms.author: raynew

---
# Support matrix for replicating from one Azure region to another

This article summarizes supported configurations and components when you deploy disaster recovery with replication, failover, and recovery of Azure VMs from one Azure region to another, using the [Azure Site Recovery](site-recovery-overview.md) service.


## Deployment method support

**Deployment method** |  **Supported / Not supported**
--- | ---
**Azure portal** | Supported
**PowerShell** | [Azure to Azure replication with PowerShell](azure-to-azure-powershell.md)
**REST API** | Supported
**CLI** | Not currently supported


## Resource support

**Resource action** | **Details**
--- | --- | ---
**Move vault across resource groups** | Not supported
**Move compute/storage/network resources across resource groups** | Not supported.<br/><br/> If you move a VM or associated components such as storage/network after the VM is replicating, you need to disable and then re-enable replication for the VM.
**Replicate Azure VMs from one subscription to another for disaster recovery** | Supported within the same Azure Active Directory tenant.
**Migrate VMs across regions within supported geographical clusters (within and across subscriptions)** | Supported within the same Azure Active Directory tenant.
**Migrate VMs within the same region** | Not supported.

## Region support

You can replicate and recover VMs between any two regions within the same geographic cluster. Geographic clusters are defined keeping data latency and sovereignty in mind.


**Geographic cluster** | **Azure regions**
-- | --
America | Canada East, Canada Central, South Central US, West Central US, East US, East US 2, West US, West US 2, Central US, North Central US
Europe | UK West, UK South, North Europe, West Europe, France Central, France South
Asia | South India, Central India, Southeast Asia, East Asia, Japan East, Japan West, Korea Central, Korea South
Australia	| Australia East, Australia Southeast, Australia Central, Australia Central 2
Azure Government	| US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Germany	| Germany Central, Germany Northeast
China | China East, China North, China North2, China East2

>[!NOTE]
>
> - For **Brazil South** region, you can replicate and fail over to one of the following: South Central US, West Central US, East US, East US 2, West US, West US 2, and North Central US regions. It should be noted that Site Recovery has only enabled Brazil South to be used as a source region from where VMs can be protected. It **cannot be used as a Target DR region** for any of the Azure regions like South Central US. The reason being latency observed due to geographical distance it is recommended to select any other America's region other than Brazil South.
>
> - If you are **not able to see a region** where you want **to create a vault** then make sure your subscription has access to create resources in that region. For example: If you are not able to create vault in France South then your subscription doesn't have access to France south region. Please file support ticket under issue Type "subscription management" and  problem type "other General questions" subject " whitelist subscription for the XXX Azure region"
>
> - If you are **not able to see a region** within a Geographic cluster **during enabling replication** then make sure your subscription has access to create virtual machine in that region. For example: If you are trying to protect virtual machines from France Central to France south and don't see France south under the region drop down then your subscription doesn't have access to deploy VM in that region. Please file support ticket under issue Type "subscription management" and  problem type "other General questions" subject " whitelist subscription for the XXX Azure region"
> - You cannot select regions across geographic clusters mentioned above.


## Cache storage

This table summarizes support for the cache storage account used by Site Recovery during replication.

**Setting** | **Support** | **Details**
--- | --- | ---
General purpose V2 storage accounts (Hot and Cool tier) | Not supported. | The limitation exists for cache storage because transaction costs for V2 are substantially higher than V1 storage accounts.
Azure Storage firewalls for virtual networks  | Supported | If you are using firewall enabled cache storage account or target storage account, ensure you ['Allow trusted Microsoft services'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## Replicated machine operating systems

Site Recovery supports replication of Azure VMs running the operating systems listed in this section.

### Windows

**Operating system** | **Details**
--- | ---
Windows Server 2016  | Server Core, Server with Desktop Experience
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Running SP1 or later

#### Linux

**Operating system** | **Details**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Supported kernel versions](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Supported kernel version](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu servers using password-based authentication and login, and the cloud-init package to configure cloud VMs, might have password-based login disabled on failover (depending on the cloudinit configuration). Password-based login can be re-enabled on the virtual machine by resetting the password from the Support > Troubleshooting > Settings menu (of the failed over VM in the Azure portal.
Debian 7 | [Supported kernel versions](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Supported kernel versions](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1,SP2,SP3. [(Supported kernel versions)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Upgrade of replicating machines from SP3 to SP4 isn't supported. If a replicated machine has been upgraded, you need to disable replication and re-enable replication after the upgrade.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Running either the Red Hat compatible kernel or Unbreakable Enterprise Kernel Release 3 (UEK3).


#### Supported Ubuntu kernel versions for Azure virtual machines

**Release** | **Mobility service version** | **Kernel version** |
--- | --- | --- |
14.04 LTS | 9.22 | 3.13.0-24-generic to 3.13.0-164-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-140-generic,<br/>4.15.0-1023-azure to 4.15.0-1036-azure |
14.04 LTS | 9.21 | 3.13.0-24-generic to 3.13.0-163-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-140-generic,<br/>4.15.0-1023-azure to 4.15.0-1035-azure |
14.04 LTS | 9.20 | 3.13.0-24-generic to 3.13.0-161-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-138-generic,<br/>4.15.0-1023-azure to 4.15.0-1030-azure |
14.04 LTS | 9.19 | 3.13.0-24-generic to 3.13.0-153-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-131-generic |
|||
16.04 LTS | 9.22 | 4.4.0-21-generic to 4.4.0-140-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-43-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1036-azure|
16.04 LTS | 9.21 | 4.4.0-21-generic to 4.4.0-140-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-42-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1035-azure|
16.04 LTS | 9.20 | 4.4.0-21-generic to 4.4.0-138-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-38-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1030-azure|
16.04 LTS | 9.19 | 4.4.0-21-generic to 4.4.0-131-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-30-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1019-azure|


#### Supported Debian kernel versions for Azure virtual machines

**Release** | **Mobility service version** | **Kernel version** |
--- | --- | --- |
Debian 7 | 9.19,9.20,9.21,9.22 | 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.20, 9.21 | 3.16.0-4-amd64 to 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.19 | 3.16.0-4-amd64 to 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.18 | 3.16.0-4-amd64 to 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.6-amd64 |

#### Supported SUSE Linux Enterprise Server 12 kernel versions for Azure virtual machines

**Release** | **Mobility service version** | **Kernel version** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.22 | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default to 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.21 | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default to 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.20 | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default to 4.4.162-94.69-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.19 | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default to 4.4.140-94.42-default |


## Replicated machines - Linux file system/guest storage

* File systems: ext3, ext4, ReiserFS (Suse Linux Enterprise Server only), XFS, BTRFS
* Volume manager: LVM2
* Multipath software: Device Mapper


## Replicated machines - compute settings

**Setting** | **Support** | **Details**
--- | --- | ---
Size | Any Azure VM size with at least 2 CPU cores and 1-GB RAM | Verify [Azure virtual machine sizes](../virtual-machines/windows/sizes.md).
Availability sets | Supported | If you enable replication for an Azure VM with the default options, an availability set is created automatically based on the source region settings. You can modify these settings.
Availability zones | Supported |
Hybrid Use Benefit (HUB) | Supported | If the source VM has a HUB license enabled, a test failover or failed over VM also uses the HUB license.
VM scale sets | Not supported |
Azure gallery images - Microsoft published | Supported | Supported if the VM runs on a supported operating system.
Azure Gallery images - Third party published | Supported | Supported if the VM runs on a supported operating system.
Custom images - Third party published | Supported | Supported if the VM runs on a supported operating system.
VMs migrated using Site Recovery | Supported | If a VMware VM or physical machine was migrated to Azure using Site Recovery, you need to uninstall the older version of Mobility service running on the machine, and restart the machine before replicating it to another Azure region.

## Replicated machines - disk actions

**Action** | **Details**
-- | ---
Resize disk on replicated VM | Supported
Add a disk to a replicated VM | Not supported.<br/><br/> You need to disable replication for the VM, add the disk, and then enable replication again.

## Replicated machines - storage

This table summarized support for the Azure VM OS disk, data disk, and temporary disk.

- It's important to observe the VM disk limits and targets for [Linux](../virtual-machines/linux/disk-scalability-targets.md) and [Windows](../virtual-machines/windows/disk-scalability-targets.md) VMs to avoid any performance issues.
- If you deploy with the default settings, Site Recovery automatically creates disks and storage accounts based on the source settings.
- If you customize, ensure you follow the guidelines.

**Component** | **Support** | **Details**
--- | --- | ---
OS disk maximum size | 2048 GB | [Learn more](../virtual-machines/windows/managed-disks-overview.md) about VM disks.
Temporary disk | Not supported | The temporary disk is always excluded from replication.<br/><br/> Don't store any persistent data on the temporary disk. [Learn more](../virtual-machines/windows/managed-disks-overview.md).
Data disk maximum size | 4095 GB |
Data disk maximum number | Up to 64, in accordance with support for a specific Azure VM size | [Learn more](../virtual-machines/windows/sizes.md) about VM sizes.
Data disk change rate | Maximum of 10 MBps per disk for premium storage. Maximum of 2 MBps per disk for Standard storage. | If the average data change rate on the disk is continuously higher than the maximum, replication won't catch up.<br/><br/>  However, if the maximum is exceeded sporadically, replication can catch up, but you might see slightly delayed recovery points.
Data disk - standard storage account | Supported |
Data disk - premium storage account | Supported | If a VM has disks spread across premium and standard storage accounts, you can select a different target storage account for each disk, to ensure you have the same storage configuration in the target region.
Managed disk - standard | Supported in Azure regions in which Azure Site Recovery is supported. |
Managed disk - premium | Supported in Azure regions in which Azure Site Recovery is supported. |
Standard SSD | Supported |
Redundancy | LRS and GRS are supported.<br/><br/> ZRS isn't supported.
Cool and hot storage | Not supported | VM disks aren't supported on cool and hot storage
Storage Spaces | Supported |
Encryption at rest (SSE) | Supported | SSE is the default setting on storage accounts.	 
Azure Disk Encryption (ADE) for Windows OS | VMs enabled for [encryption with Azure AD app](https://aka.ms/ade-aad-app) are supported |
Azure Disk Encryption (ADE) for Linux OS | Not supported |
Hot add/remove disk	| Not supported | If you add or remove data disk on the VM, you need to disable replication and enable replication again for the VM.
Exclude disk | [supported through powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) |	Temporary disk is excluded by default.
Storage Spaces Direct  | Supported for crash consistent recovery points. Application consistent recovery points are not supported. |
Scale-out File Server  | Supported for crash consistent recovery points. Application consistent recovery points are not supported. |
LRS | Supported |
GRS | Supported |
RA-GRS | Supported |
ZRS | Not supported |
Cool and Hot Storage | Not supported | Virtual machine disks are not supported on cool and hot storage
Azure Storage firewalls for Virtual networks  | Supported | If you are restricting the virtual network access to storage accounts, ensure you ['Allow trusted Microsoft services'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
General purpose V2 storage accounts (Both Hot and Cool tier) | No | Transaction costs increase substantially compared to General purpose V1 storage accounts

>[!IMPORTANT]
> Ensure that you observe the VM disk scalability and performance targets for [Linux](../virtual-machines/linux/disk-scalability-targets.md) or [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtual machines to avoid any performance issues. If you follow the default settings, Site Recovery will create the required disks and storage accounts based on the source configuration. If you customize and select your own settings, ensure that you follow the disk scalability and performance targets for your source VMs.

## Azure Site Recovery limits to replicate Data change rates
The following table provides the Azure Site Recovery limits. These limits are based on our tests, but they cannot cover all possible application I/O combinations. Actual results can vary based on your application I/O mix. We should also note that there are two limits to consider, per disk data churn and per virtual machine data churn.
For example, if we look at Premium P20 disk in the below table, Site Recovery can handle 5 MB/s churn per disk with at max of five such disks per VM due to the limit of 25 MB/s total churn per VM.

**Replication storage target** | **Average source disk I/O size** |**Average source disk data churn** | **Total source disk data churn per day**
---|---|---|---
Standard storage | 8 KB	| 2 MB/s | 168 GB per disk
Premium P10 or P15 disk | 8 KB	| 2 MB/s | 168 GB per disk
Premium P10 or P15 disk | 16 KB | 4 MB/s |	336 GB per disk
Premium P10 or P15 disk | 32 KB or greater | 8 MB/s | 672 GB per disk
Premium P20 or P30 or P40 or P50 disk | 8 KB	| 5 MB/s | 421 GB per disk
Premium P20 or P30 or P40 or P50 disk | 16 KB or greater |10 MB/s | 842 GB per disk
## Replicated machines - networking
**Configuration** | **Support** | **Details**
--- | --- | ---
NIC | Maximum number supported for a specific Azure VM size | NICs are created when the VM is created during failover.<br/><br/> The number of NICs on the failover VM depends on the number of NICs on the source VM when replication was enabled. If you add or remove a NIC after enabling replication, it doesn't impact the number of NICs on the replicated VM after failover.
Internet Load Balancer | Supported | Associate the preconfigured load balancer using an Azure Automation script in a recovery plan.
Internal Load balancer | Supported | Associate the preconfigured load balancer using an Azure Automation script in a recovery plan.
Public IP address | Supported | Associate an existing public IP address with the NIC. Or, create a public IP address and associate it with the NIC using an Azure Automation script in a recovery plan.
NSG on NIC | Supported | Associate the NSG with the NIC using an Azure Automation script in a recovery plan.
NSG on subnet | Supported | Associate the NSG with the subnet using an Azure Automation script in a recovery plan.
Reserved (static) IP address | Supported | If the NIC on the source VM has a static IP address, and the target subnet has the same IP address available, it's assigned to the failed over VM.<br/><br/> If the target subnet doesn't have the same IP address available, one of the available IP addresses in the subnet is reserved for the VM.<br/><br/> You can also specify a fixed IP address and subnet in **Replicated items** > **Settings** > **Compute and Network** > **Network interfaces**.
Dynamic IP address | Supported | If the NIC on the source has dynamic IP addressing, the NIC on the failed over VM is also dynamic by default.<br/><br/> You can modify this to a fixed IP address if required.
Traffic Manager 	| Supported | You can preconfigure Traffic Manager so that traffic is routed to the endpoint in the source region on a regular basis, and to the endpoint in the target region in case of failover.
Azure DNS | Supported |
Custom DNS	| Supported |
Unauthenticated Proxy | Supported | Refer to [networking guidance document.](site-recovery-azure-to-azure-networking-guidance.md)	 
Authenticated Proxy | Not supported | If the VM is using an authenticated proxy for outbound connectivity, it cannot be replicated using Azure Site Recovery.	 
Site to Site VPN with on-premises (with or without ExpressRoute)| Supported | Ensure that the UDRs and NSGs are configured in such a way that the Site recovery traffic is not routed to on-premises. Refer to [networking guidance document.](site-recovery-azure-to-azure-networking-guidance.md)	 
VNET to VNET connection	| Supported | Refer to [networking guidance document.](site-recovery-azure-to-azure-networking-guidance.md)	 
Virtual Network Service Endpoints | Supported | If you are restricting the virtual network access to storage accounts, ensure that the trusted Microsoft services are allowed access to the storage account.
Accelerated Networking | Supported | Accelerated Networking must be enabled on source VM. [Learn more](azure-vm-disaster-recovery-with-accelerated-networking.md).



## Next steps
- Read [networking guidance for replicating Azure VMs](site-recovery-azure-to-azure-networking-guidance.md).
- Deploy disaster recovery by [replicating Azure VMs](site-recovery-azure-to-azure.md).
