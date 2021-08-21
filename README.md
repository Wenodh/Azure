# Azure

[Introduction to Azure fundamentals](https://docs.microsoft.com/en-us/learn/modules/intro-to-azure-fundamentals/)
 
[Azure Regions](https://azure.microsoft.com/en-in/global-infrastructure/geographies/)

[Create a Azure free trail account](https://k21academy.com/microsoft-azure/create-free-microsoft-azure-trial-account/)
	
[Create a Azure free trail account](https://docs.microsoft.com/en-us/learn/modules/create-an-azure-account/)

[Azure portal demo](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-overview)

[Azure pricing calculator](https://azure.microsoft.com/en-in/pricing/calculator/)

[Azure management groups](https://docs.microsoft.com/en-us/azure/governance/management-groups/overview)

[Azure management groups](https://francescomolfese.it/en/2019/07/azure-governance-come-organizzare-le-risorse-utilizzando-gli-azure-management-groups/)

[Azure updates](https://azure.microsoft.com/en-us/updates/)

[Azure subscription](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/decision-guides/subscriptions/)

[Azure RBAC (Role based access control)](https://docs.microsoft.com/en-us/azure/role-based-access-control/)

[Grant a user access to Azure resources using the Azure portal](https://docs.microsoft.com/en-us/azure/role-based-access-control/quickstart-assign-role-user-portal)

[Azure subscription roles (Built-in)](https://docs.microsoft.com/en-us/azure/role-based-access-control/rbac-and-directory-admin-roles)

[Azure custom roles](https://docs.microsoft.com/en-us/azure/role-based-access-control/custom-roles)

[Azure Tags](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources?tabs=json)

[Azure subscription cost analysis](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/quick-acm-cost-analysis?tabs=azure-portal)

[Azure Policy](https://docs.microsoft.com/en-us/azure/governance/policy/)

[Create a policy assignment to identify non-compliant resources](https://docs.microsoft.com/en-us/azure/governance/policy/assign-policy-portal)

[Azure subscription support plans](https://azure.microsoft.com/en-in/support/plans/)

[Azure Lock types](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources?tabs=json)

https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.2.1

Azure virtual Machines https://docs.microsoft.com/en-us/azure/virtual-machines/

Create a Windows virtual machine in the Azure portal https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal

Sizes for virtual machines in Azure https://docs.microsoft.com/en-us/azure/virtual-machines/sizes

Availability options for Azure Virtual Machines https://docs.microsoft.com/en-us/azure/virtual-machines/availability

Availability sets overview https://docs.microsoft.com/en-us/azure/virtual-machines/availability-set-overview

Availability Zones in Azure https://docs.microsoft.com/en-us/azure/availability-zones/az-overview?context=/azure/virtual-machines/context/context

Create a Windows virtual machine in Azure with PowerShell https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell

Create a Windows virtual machine with the Azure CLI https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-cli

Lab :

1. Assign a policy to restrict resource creation apart from East US region.
2. Explore the options for creating support request to Microsoft.
3. Assign resource lock (both read-only and delete lock

[11:33, 30/7/2021]: Create Azure VM Using Powershell:
=================================

New-AzResourceGroup -Name "PS-rg-01" -Location "Southeast asia"

New-AzVm `
    -ResourceGroupName "PS-rg-01" `
    -Name "PS-VM-01" `
	-ComputerName "MainComputer" `
    -Location "Southeast asia" `
    -VirtualNetworkName "PS-VNET-01" `
    -SubnetName "PS-SNET-01" `
    -SecurityGroupName "PS-NSG-01" `
    -PublicIpAddressName "PS-Pubip-01" `
    -OpenPorts 80,3389
	
Get-AzPublicIpAddress -ResourceGroupName "PS-rg-01" | Select "IpAddress"

mstsc /v:Publicipaddress

Remove-AzResourceGroup -Name "PS-rg-01"
[11:33, 30/7/2021] : Create Azure VM using CLI:
==========================

az group create --name "CLI-RG-01" --location "westus2"

az vm create \
    --resource-group CLI-RG-01 \
    --name CLi-VM-01 \
    --image win2019datacenter \
    --admin-username azureuser

az vm open-port --port 80 --resource-group CLI-RG-01 --name CLi-VM-01

mstsc /v:PublicIpaddress

Install-WindowsFeature -name Web-Server -IncludeManagementTools

az group delete --name "CLI-RG-01"

Labs:

1. Increased the OS disk size from 127 GB to 200 GB
2. Add new data disk (10 GB) and allocated to VM using portal
3. Add new data disk (20 GB) and allocated to VM using powershell
4. Resize Azure VM (Increase/Decrease) - Standard_D2s_V3 using portal
5. Resize Azure VM (Increase/Decrease) - Standard_D2s_V3 using powershell

[12:08, 2/8/2021] : 1. Resize a virtual machine using the Azure portal or PowerShell
   https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm

2. Attach a managed data disk to a Windows VM by using the Azure portal
   https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-managed-disk-portal

3. Attach a data disk to a Windows VM with PowerShell
   https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-disk-ps
   
[12:11, 2/8/2021] : Azure VM data disk Powershell:
=============================

$rgName = 'test-rg-01'
$vmName = 'test-vm-01'
$location = 'Central US'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk2'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 200 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 2

Update-AzVM -VM $vm -ResourceGroupName $rgName
[12:12, 2/8/2021] : Azure VM Resize powershell:
==========================

$resourceGroup = "test-rg-01"
$vmName = "test-vm-01"

Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName

$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "Standard_D2s_v3"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup

[12:03, 3/8/2021]  1. Troubleshoot Remote Desktop connections to an Azure virtual machine
   https://docs.microsoft.com/en-us/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection
  
   https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms

2. Run PowerShell scripts in your Windows VM by using Run Command
   https://docs.microsoft.com/en-us/azure/virtual-machines/windows/run-command

3. Create a Linux virtual machine in the Azure portal
   https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal

4. Create a Linux virtual machine in Azure with PowerShell
   https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-powershell

5. Create a Linux virtual machine with the Azure CLI
   https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-cli

6. Donwload git 
   https://git-scm.com/downloads

7. Download putty and puttygen
   https://www.putty.org/
   https://www.puttygen.com/
   
[12:05, 3/8/2021]: Labs:

1. Look for the Azure VM connectivity issues (RDP Issue)
2. Create Azure Linux VM (ubuntu) using portal
3. Create Azure Linux VM (ubuntu) using Azure powershell
4. Create Azure Linux VM (ubuntu) using Azure CLI
5. Connect to Linux VM using powershell, GitBash and Putty

[11:52, 5/8/2021] : 1. Linux Basic commands
   https://www.javatpoint.com/linux-commands

2. Azure Bastion documentation
   https://docs.microsoft.com/en-us/azure/bastion/

3. How to enable bastion for Azure VMs
   https://docs.microsoft.com/en-us/azure/bastion/quickstart-host-portal
[11:52, 5/8/2021]  Lab:

1. Explore all the basic commands of Linux
2. Enable AzureBastion for Azure Windows VM and connect
3. Enable AzureBastion for Azure Linux VM and connect

[11:41, 6/8/2021]  1. Azure Monitor
   https://docs.microsoft.com/en-us/azure/azure-monitor/

2. Monitor an Azure resource with Azure Monitor
   https://docs.microsoft.com/en-us/azure/azure-monitor/essentials/quick-monitor-azure-resource

3. Create, view, and manage metric alerts using Azure Monitor
   https://docs.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-metric
[11:43, 6/8/2021] 

Lab:

1. Setup Monitoring for both Windows and Linux using azure monitor
2. Create Alerts (Percentage CPU and All Administrative activity) for Azure VM's and test it

[11:24, 7/8/2021] : 1. Azure Backup service documentation
   https://docs.microsoft.com/en-us/azure/backup/backup-overview

2. Azure Backup types
   https://docs.microsoft.com/en-us/azure/backup/backup-architecture

3. Back up a virtual machine in Azure
   https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-portal

4. Back up a virtual machine in Azure with PowerShell
   https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-powershell

5. Use Azure portal to back up multiple virtual machines
   https://docs.microsoft.com/en-us/azure/backup/tutorial-backup-vm-at-scale

6. How to restore Azure VM data in Azure portal
   https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms

7. Troubleshooting backup failures on Azure virtual machines
   https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-troubleshoot
[11:24, 7/8/2021] Rk 2 Rama Krishna: 8. Back up Windows Server files and folders to Azure
   https://docs.microsoft.com/en-us/azure/backup/install-mars-agent#
  
   https://docs.microsoft.com/en-us/azure/backup/backup-windows-with-mars-agent

9. Restore files to Windows Server using the MARS Agent
   https://docs.microsoft.com/en-us/azure/backup/backup-azure-restore-windows-server
   
   Agenda:  RSV (Recovery service vault)
=======

Azure Backup for Virtual Machines:

1. RSV (Recovery service vault)
2. Backup server
3. Adding Azure VM's (Windows/linux)
4. Policy 
5. Enable Backup
6. VM will taken as snapshot and stored in the Vault
7. Restore it using snapshot if vm is corrupted/deleted


Backup of Onprem server files and folders:

1. RSV (Recovery service vault)
2. Backup of onpremserver files and folders
3. Prepare infrastructure
4. Downloading MARS agent and vault credentials
5. Installing agent and performing registration to vault
6. Backup of files and folder
7. Recover the files if somebody deleted.

Lab:

1. Take a Backup of Azure VM using RSV (portal)
2. Take a Backup of Azure VM using RSV (powershell)
3. Take a Backup of Onprem server files and folders 
4. Restore the VM using snapshot
5. Restore the files and folders

[11:11, 9/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-powershell
[11:11, 9/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-cli
[11:23, 9/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm
[11:26, 9/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-

[06:56, 11/8/2021] Rk 2 Rama Krishna: 1. Azure SQL Database
   https://docs.microsoft.com/en-us/azure/azure-sql/

2. Create SQL Server 2019 on a Windows virtual machine in the Azure portal
   https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart

   https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal

3. Create SQL Server on a Windows virtual machine with Azure PowerShell
   https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-vm-create-powershell-quickstart

   https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/create-sql-vm-powershell

4. Connect to a SQL Server virtual machine on Azure
   https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machine…
[06:56, 11/8/2021] Rk 2 Rama Krishna: Lab:

1. Create SQL Server in Azure VM Using portal
2. Create SQL Server in Azure VM using powershell
3. Connect to sql server and create new user
4. Backup sql server in azure vm using portal
5. Backup sql server in azure vm using powershell
[11:18, 11/8/2021] Rk 2 Rama Krishna: 1. Remove machine specific information by generalizing a VM before creating an image
   https://docs.microsoft.com/en-us/azure/virtual-machines/generalize

2. Create a managed image of a generalized VM in Azure
   https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource

3. Shared Image Galleries overview
   https://docs.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries

4. Virtual Machine Scale Sets documentation
   https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/
[11:19, 11/8/2021] Rk 2 Rama Krishna: 1. Create an Image from Azure windows VM using portal/powershell
2. Create an IMage from Azure linux VM 
3. Create Azure VMSS (Virtual machine scale set) using portal/powershell
[11:05, 12/8/2021] Rk 2 Rama Krishna: Storage Accounts:
================

# Types of Storage accounts:

1. Standard general-purpose v2
2. Premium block blobs
3. Premium file shares
4. Premium page blobs

# Types storage services:

1. Blob Storage (Binary large object)
	page blob
	append blob
	block blob
2. File share
3. Queues
4. Table 

# Azure Storage redundancy:

1. LRS (Locally-redundant storage) - 3 copies in signle region
2. ZRS (Zone-redundant storage) - 3 copies in different zones in a single region
3. GRS (Geo-redundant storage) - 6 copies,3 copies in primary 3 copies in secondary
4. RA-GRS (ReadAccess-Geo-redundant storage) - 6 copies, read access in secondary
5. GZRS (Geo-Zone-redundant storage) - 6 copies
6. RA-GZRS (ReadAccess-Geo-zone-redundant storage) - 6 copies


Access tiers:

1. Hot - Frequently accessing the data
2. Cool - Infrequently accessing the data
3. Archive - Not in use

Performance tiers:

1. Standard
2. Premium
[11:06, 12/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview#performance-tiers
[11:07, 12/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction?toc=/azure/storage/blobs/toc.json
[11:07, 12/8/2021] Rk 2 Rama Krishna: https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json#locally-redundant-storage
[11:07, 12/8/2021] Rk 2 Rama Krishna: https://azure.microsoft.com/en-in/pricing/details/storage/blobs/

[11:21, 13/8/2021] Rk 2 Rama Krishna: 1. Azure blob storage
   https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction

2. Azure fileshare
   https://docs.microsoft.com/en-us/azure/storage/files/storage-files-introduction

3. Mount SMB Azure file share on Windows
   https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-windows

4. Mount SMB Azure file share on Linux
   https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-linux?tabs=smb311

5. Create azure storage account using powershell
   https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-powershell

6. Create azure storage account using azurecli
   https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-cli
[11:23, 13/8/2021] Rk 2 Rama Krishna: Lab:

1. Create Azure storage account using portal
2. Create Azure storage account using powershell
3. Create Azure storage account using CLI
4. Upload some files to azure blob storage creating a container
5. create a fileshare and upload data via portal
6. Mount the fileshare to Azure windows VM
7. Mount the filesahre to Azure linux VM

[11:12, 14/8/2021] Rk 2 Rama Krishna: 1. Download storage explorer
   https://azure.microsoft.com/en-in/features/storage-explorer/#overview

2. Azure storage explorer import/export data
   https://docs.microsoft.com/en-us/azure/import-export/storage-import-export-service
   
   https://docs.microsoft.com/en-us/azure/import-export/storage-import-export-data-from-blobs?tabs=azure-portal

3. Download AzCopy
   https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10

4. Authorize access to blobs with AzCopy and Azure Active Directory (Azure AD)
   https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-authorize-azure-active-directory

5. Upload files to Azure Blob storage by using AzCopy
   https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcop…
[11:13, 14/8/2021] Rk 2 Rama Krishna: AzCopy Commands:
===============

#Login to Azure using AzCopy
azcopy login --tenant-id=05d922d4-9527-4e1d-99a8-aa839c572673

#Create a container in Azure Blob storage
azcopy make "https://saaug01.blob.core.windows.net/container"

#upload a file
azcopy copy "C:\Users\Dell\Desktop\ATT.txt" "https://saaug01.blob.core.windows.net/container/ATT.txt?sv=2020-08-04&ss=bfqt&srt=sco&sp=rwdlacuptfx&se=2021-08-31T13:15:54Z&st=2021-08-14T05:15:54Z&spr=https&sig=aPRpikCQpH5r0x3T3dS6bIi4%2FwHgUhNexdLYYeRpjDQ%3D"

#Upload a directory
azcopy copy "C:\Users\Dell\Desktop\Ansible" "https://saaug01.blob.core.windows.net/container/Ansible?sv=2020-08-04&ss=bfqt&srt=sco&sp=rwdlacuptfx&se=2021-08-31T13:15:54Z&st=2021-08-14T05:15:54Z&spr=https&sig=aPRpikCQpH5r0x3T3dS6bIi4%2FwHgUhNex…
[19/8 11:40] Rk 2 Rama Krishna: Virtual Network:
===============

1. Virtual Network (VNET)

   10.10.0.0/24 = 256

VNET1 - 10.10.0.0/24 - 256 (5 Resered IP's)

	10.10.0.0 : Network address
	10.10.0.1 : Reserved by Azure for the default gateway
	10.10.0.2 : Reserved by Azure to map the Azure DNS IPs to the VNet space
	10.10.0.3 : Reserved by Azure to map the Azure DNS IPs to the VNet space
	10.10.0.4
	.
	.
	.
	10.10.0.254
	10.10.0.255 : Network broadcast address for subnets of size /25 and larger. This will be a different address in smaller subnets.
[19/8 11:41] Rk 2 Rama Krishna: VNET Peering:
=============

1. Regional VNET Peering (Same region)
2. Global VNET Peering (Diff region vnet's)
[19/8 11:44] Rk 2 Rama Krishna: 1. Azure Virtual Network
   https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview

2. Create a virtual network using the Azure portal
   https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-portal

3. Create a virtual network using PowerShell
   https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-powershell

4. Create a virtual network using the Azure CLI
   https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-cli

5. Virtual network peering
   https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview

6. Create, change, or delete a virtual network peering
   https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-peering
[20/8 11:13] Rk 2 Rama Krishna: 1. Create a virtual network peering - Resource Manager, different subscriptions and Azure Active Directory tenants

   https://docs.microsoft.com/en-us/azure/virtual-network/create-peering-different-subscriptions

2. Configure a VNet-to-VNet VPN gateway connection by using the Azure portal

   https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal

3. Policy-based VPN VS Route-based VPN
   https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps?WT.mc_id=Portal-Microsoft_Azure_HybridNetworking

#Benifits

1.High Availability,Fault Tolerance and Disaster Recovery.

- Availability Zones
- Availabilty sets
- Virtual Machine Scale Sets (VMSS)

2.Scalability and Elasticity.

3.Business Agility.

4.Economies of Scale.

5.Capital Expenditure(CapEx) and Operational Expenditure (OpEx).

6.The Consumption-Based.

On-premises
Iaas (Infrastructure as a service) System Managers
Paas (Platform as a service) Developers and Deployers
Saas (Sofware as a service) Business User

##cloud types

1>Public Cloud

2>Private Cloud

3>Gov Cloud

#Interview Questions

https://www.dotnettricks.com/learn/azure/top-20-azure-administrator-interview-questions-answers

https://www.dotnettricks.com/learn/azure/top-20-azure-developer-interview-questions-answers

https://technicalinfobybinit.blogspot.com/2021/07/azure-administrator-interview-questions.html

https://www.sqlshack.com/microsoft-azure-administrator-scenario-related-interview-questions-and-answers/

https://www.edureka.co/blog/interview-questions/azure-interview-questions/

https://www.infosectrain.com/blog/top-20-azure-administrator-interview-questions/

https://www.interviewbit.com/azure-interview-questions/

https://intellipaat.com/blog/interview-question/microsoft-azure-interview-questions/

https://www.simplilearn.com/tutorials/azure-tutorial/azure-interview-questions?source=sl_frs_nav_playlist_video_clicked

https://www.guru99.com/azure-interview-questions-answers.html

There are quota limits on each subscription that can impact VM creation. By default, you cannot have more than 20 virtual cores across all VMs within a region. You can either split up VMs across regions or file an online request to increase your limits.

The temporary disk is not persistent. You should only write data to this disk that you are willing to lose at any time.
