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
