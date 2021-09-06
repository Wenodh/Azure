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

<https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.2.1>

Azure virtual Machines <https://docs.microsoft.com/en-us/azure/virtual-machines/>

Create a Windows virtual machine in the Azure portal <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal>

Sizes for virtual machines in Azure <https://docs.microsoft.com/en-us/azure/virtual-machines/sizes>

Availability options for Azure Virtual Machines <https://docs.microsoft.com/en-us/azure/virtual-machines/availability>

Availability sets overview <https://docs.microsoft.com/en-us/azure/virtual-machines/availability-set-overview>

Availability Zones in Azure <https://docs.microsoft.com/en-us/azure/availability-zones/az-overview?context=/azure/virtual-machines/context/context>

Create a Windows virtual machine in Azure with PowerShell <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell>

Create a Windows virtual machine with the Azure CLI <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-cli>

Lab :

1. Assign a policy to restrict resource creation apart from East US region.
2. Explore the options for creating support request to Microsoft.
3. Assign resource lock (both read-only and delete lock

# [11:33, 30/7/2021]: Create Azure VM Using Powershell

New-AzResourceGroup -Name "PS-rg-01" -Location "Southeast asia"

New-AzVm `-ResourceGroupName "PS-rg-01"`
-Name "PS-VM-01" `-ComputerName "MainComputer"`
-Location "Southeast asia" `-VirtualNetworkName "PS-VNET-01"`
-SubnetName "PS-SNET-01" `-SecurityGroupName "PS-NSG-01"`
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
<https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm>

2. Attach a managed data disk to a Windows VM by using the Azure portal
   <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-managed-disk-portal>

3. Attach a data disk to a Windows VM with PowerShell
   <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-disk-ps>

# [12:11, 2/8/2021] : Azure VM data disk Powershell

$rgName = 'test-rg-01'
$vmName = 'test-vm-01'
$location = 'Central US'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '\_datadisk2'

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

[12:03, 3/8/2021] 1. Troubleshoot Remote Desktop connections to an Azure virtual machine
<https://docs.microsoft.com/en-us/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection>

<https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms>

2. Run PowerShell scripts in your Windows VM by using Run Command
   <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/run-command>

3. Create a Linux virtual machine in the Azure portal
   <https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal>

4. Create a Linux virtual machine in Azure with PowerShell
   <https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-powershell>

5. Create a Linux virtual machine with the Azure CLI
   <https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-cli>

6. Donwload git
   <https://git-scm.com/downloads>

7. Download putty and puttygen
   <https://www.putty.org/>
   <https://www.puttygen.com/>

[12:05, 3/8/2021]: Labs:

1. Look for the Azure VM connectivity issues (RDP Issue)
2. Create Azure Linux VM (ubuntu) using portal
3. Create Azure Linux VM (ubuntu) using Azure powershell
4. Create Azure Linux VM (ubuntu) using Azure CLI
5. Connect to Linux VM using powershell, GitBash and Putty

[11:52, 5/8/2021] : 1. Linux Basic commands
<https://www.javatpoint.com/linux-commands>

2. Azure Bastion documentation
   <https://docs.microsoft.com/en-us/azure/bastion/>

3. How to enable bastion for Azure VMs
   <https://docs.microsoft.com/en-us/azure/bastion/quickstart-host-portal>
   [11:52, 5/8/2021] Lab:

4. Explore all the basic commands of Linux
5. Enable AzureBastion for Azure Windows VM and connect
6. Enable AzureBastion for Azure Linux VM and connect

[11:41, 6/8/2021] 1. Azure Monitor
<https://docs.microsoft.com/en-us/azure/azure-monitor/>

2. Monitor an Azure resource with Azure Monitor
   <https://docs.microsoft.com/en-us/azure/azure-monitor/essentials/quick-monitor-azure-resource>

3. Create, view, and manage metric alerts using Azure Monitor
   <https://docs.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-metric>
   [11:43, 6/8/2021]

Lab:

1. Setup Monitoring for both Windows and Linux using azure monitor
2. Create Alerts (Percentage CPU and All Administrative activity) for Azure VM's and test it

[11:24, 7/8/2021] : 1. Azure Backup service documentation
<https://docs.microsoft.com/en-us/azure/backup/backup-overview>

2. Azure Backup types
   <https://docs.microsoft.com/en-us/azure/backup/backup-architecture>

3. Back up a virtual machine in Azure
   <https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-portal>

4. Back up a virtual machine in Azure with PowerShell
   <https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-powershell>

5. Use Azure portal to back up multiple virtual machines
   <https://docs.microsoft.com/en-us/azure/backup/tutorial-backup-vm-at-scale>

6. How to restore Azure VM data in Azure portal
   <https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms>

7. Troubleshooting backup failures on Azure virtual machines
   <https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-troubleshoot>
   [11:24, 7/8/2021] Rk 2 Rama Krishna: 8. Back up Windows Server files and folders to Azure
   <https://docs.microsoft.com/en-us/azure/backup/install-mars-agent>#

    <https://docs.microsoft.com/en-us/azure/backup/backup-windows-with-mars-agent>

8. Restore files to Windows Server using the MARS Agent
   <https://docs.microsoft.com/en-us/azure/backup/backup-azure-restore-windows-server>

# Agenda: RSV (Recovery service vault)

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

[11:11, 9/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-powershell>
[11:11, 9/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-cli>
[11:23, 9/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm>
[11:26, 9/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms>-

[06:56, 11/8/2021] Rk 2 Rama Krishna: 1. Azure SQL Database
<https://docs.microsoft.com/en-us/azure/azure-sql/>

2. Create SQL Server 2019 on a Windows virtual machine in the Azure portal
   <https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart>

    <https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal>

3. Create SQL Server on a Windows virtual machine with Azure PowerShell
   <https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-vm-create-powershell-quickstart>

    <https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/create-sql-vm-powershell>

4. Connect to a SQL Server virtual machine on Azure
   <https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machine>…
   [06:56, 11/8/2021] Rk 2 Rama Krishna: Lab:

5. Create SQL Server in Azure VM Using portal
6. Create SQL Server in Azure VM using powershell
7. Connect to sql server and create new user
8. Backup sql server in azure vm using portal
9. Backup sql server in azure vm using powershell
   [11:18, 11/8/2021] Rk 2 Rama Krishna: 1. Remove machine specific information by generalizing a VM before creating an image
   <https://docs.microsoft.com/en-us/azure/virtual-machines/generalize>

10. Create a managed image of a generalized VM in Azure
    <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource>

11. Shared Image Galleries overview
    <https://docs.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries>

12. Virtual Machine Scale Sets documentation
    <https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/>
    [11:19, 11/8/2021] Rk 2 Rama Krishna: 1. Create an Image from Azure windows VM using portal/powershell
13. Create an IMage from Azure linux VM

14. Create Azure VMSS (Virtual machine scale set) using portal/powershell
    [11:05, 12/8/2021] Rk 2 Rama Krishna: Storage Accounts:

    ================

Types of Storage accounts:

1. Standard general-purpose v2
2. Premium block blobs
3. Premium file shares
4. Premium page blobs

Types storage services:

1. Blob Storage (Binary large object)
   page blob
   append blob
   block blob
2. File share
3. Queues
4. Table

Azure Storage redundancy:

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
   [11:06, 12/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview#performance-tiers>
   [11:07, 12/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction?toc=/azure/storage/blobs/toc.json>
   [11:07, 12/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json#locally-redundant-storage>
   [11:07, 12/8/2021] Rk 2 Rama Krishna: <https://azure.microsoft.com/en-in/pricing/details/storage/blobs/>

[11:21, 13/8/2021] Rk 2 Rama Krishna: 1. Azure blob storage
<https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction>

2. Azure fileshare
   <https://docs.microsoft.com/en-us/azure/storage/files/storage-files-introduction>

3. Mount SMB Azure file share on Windows
   <https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-windows>

4. Mount SMB Azure file share on Linux
   <https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-linux?tabs=smb311>

5. Create azure storage account using powershell
   <https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-powershell>

6. Create azure storage account using azurecli
   <https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-cli>
   [11:23, 13/8/2021] Rk 2 Rama Krishna: Lab:

7. Create Azure storage account using portal
8. Create Azure storage account using powershell
9. Create Azure storage account using CLI
10. Upload some files to azure blob storage creating a container
11. create a fileshare and upload data via portal
12. Mount the fileshare to Azure windows VM
13. Mount the filesahre to Azure linux VM

[11:12, 14/8/2021] Rk 2 Rama Krishna: 1. Download storage explorer
<https://azure.microsoft.com/en-in/features/storage-explorer/#overview>

2. Azure storage explorer import/export data
   <https://docs.microsoft.com/en-us/azure/import-export/storage-import-export-service>

    <https://docs.microsoft.com/en-us/azure/import-export/storage-import-export-data-from-blobs?tabs=azure-portal>

3. Download AzCopy
   <https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10>

4. Authorize access to blobs with AzCopy and Azure Active Directory (Azure AD)
   <https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-authorize-azure-active-directory>

5. Upload files to Azure Blob storage by using AzCopy
   <https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcop>…
   [11:13, 14/8/2021] Rk 2 Rama Krishna: AzCopy Commands:

    ===============

# Login to Azure using AzCopy

azcopy login --tenant-id=05d922d4-9527-4e1d-99a8-aa839c572673

# Create a container in Azure Blob storage

azcopy make "https://saaug01.blob.core.windows.net/container"

# upload a file

azcopy copy "C:\Users\Dell\Desktop\ATT.txt" "https://saaug01.blob.core.windows.net/container/ATT.txt?sv=2020-08-04&ss=bfqt&srt=sco&sp=rwdlacuptfx&se=2021-08-31T13:15:54Z&st=2021-08-14T05:15:54Z&spr=https&sig=aPRpikCQpH5r0x3T3dS6bIi4%2FwHgUhNexdLYYeRpjDQ%3D"

# Upload a directory

azcopy copy "C:\Users\Dell\Desktop\Ansible" "<https://saaug01.blob.core.windows.net/container/Ansible?sv=2020-08-04&ss=bfqt&srt=sco&sp=rwdlacuptfx&se=2021-08-31T13:15:54Z&st=2021-08-14T05:15:54Z&spr=https&sig=aPRpikCQpH5r0x3T3dS6bIi4%2FwHgUhNex>…

[11:27, 23/8/2021] Rk 2 Rama Krishna: Azure Virtual Network Connectivity
[11:31, 23/8/2021] Rk 2 Rama Krishna: Site-to-Site VPN
[11:31, 23/8/2021] Rk 2 Rama Krishna: Point-to-Site VPN
[11:31, 23/8/2021] Rk 2 Rama Krishna: 1. Choosing between Azure VNet Peering and VNet Gateways
<https://azure.microsoft.com/en-in/blog/vnet-peering-and-vpn-gateways/>

2. Create a Site-to-Site connection in the Azure portal
   <https://docs.microsoft.com/en-us/azure/vpn-gateway/tutorial-site-to-site-portal>

3. Configure a Point-to-Site VPN connection using Azure certificate authentication: Azure portal
   <https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal>

4. Generate and export certificates for Point-to-Site using PowerShell
   <https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-certificates-point-to-site>
   [11:32, 23/8/2021] Rk 2 Rama Krishna: Lab:

5. Configure a VNet-to-VNet VPN gateway connection by using the Azure portal
6. Configure a Point-to-Site VPN connection using Azure certificate authentication: Azure portal
   [11:13, 24/8/2021] Rk 2 Rama Krishna: 1. Azure Network security group
   <https://docs.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview>

7. Azure Network interface
   <https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-network-interface>

8. Azure Public ip
   <https://docs.microsoft.com/en-us/azure/virtual-network/public-ip-addresses>

9. Azure Traffic Analytics
   <https://docs.microsoft.com/en-us/azure/network-watcher/traffic-analytics>

10. Azure network watcher
    <https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview>
    [11:15, 24/8/2021] Rk 2 Rama Krishna: <https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction>

[10:09, 25/8/2021] Rk 2 Rama Krishna: 1. Azure Log Analytics workspace
<https://docs.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-tutorial>

2. Log queries in Azure Monitor
   <https://docs.microsoft.com/en-us/azure/azure-monitor/logs/log-query-overview>

    <https://docs.microsoft.com/en-us/azure/azure-monitor/logs/examples>

    <https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer>

3. Azure Traffic Analytics
   <https://docs.microsoft.com/en-us/azure/network-watcher/traffic-analytics>

4. Introduction to flow logging for network security groups
   <https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview>

5. Azure Keyvault
   <https://docs.microsoft.com/en-in/azure/key-vault/general/overview>
   [11:11, 26/8/2021] Rk 2 Rama Krishna: 1. Azure Active directory
   <https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis>

6. Compare Active Directory to Azure Active Directory
   <https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-compare-azure-ad-to-ad>

7. What are the Azure AD licenses?
   <https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis>

8. Add or delete users using Azure Active Directory
   <https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-users-azure-active-directory>

9. Add or delete users using Azure Active Directory
   <https://docs.microsoft.com/bs-latn-ba/azure/active-directory/fundamentals/add-users-azure-active-directory>

10. Reset a user's password using Azure Active Directory
    <https://docs.microsoft.com/bs-latn-ba/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal>

11. Create a basic group and add members using Azure Active Directory
    <https://docs.microsoft.com/bs-latn-ba/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal>

12. Overview of Azure AD Multi-Factor Authentication for your organization
    <https://docs.microsoft.com/bs-latn-ba/azure/active-directory/fundamentals/concept-fundamentals-mfa-get-started>

13. Azure AD built-in roles
    <https://docs.microsoft.com/en-us/azure/active-directory/roles/permissions-reference>

14. Application and service principal objects in Azure Active Directory
    <https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals>

15. Sign in with a service principal
    <https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli>
[31/8 11:24] Rk 2 Rama Krishna: 1. Azure App Service
   https://docs.microsoft.com/en-us/azure/app-service/overview

2. Introduction to the App Service Environments
   https://docs.microsoft.com/en-us/azure/app-service/environment/intro

3. Deploy an ASP.NET web app using Visualstudio code
   https://docs.microsoft.com/en-us/azure/app-service/quickstart-dotnetcore?tabs=netcore31&pivots=development-environment-vscode

4. Set up staging environments in Azure App Service
   https://docs.microsoft.com/en-us/azure/app-service/deploy-staging-slots

5. Azure App Service plan overview
   https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans
[31/8 12:12] Rk 2 Rama Krishna: Lab:

1. Create app service (web app) 
2. Deploy a ASP. NET application using visual studio code
3. Create a new deployment slot and swap the code
4. Look for the trouble shooting options
[1/9 11:03] Rk 2 Rama Krishna: 1. Configure your App Service or Azure Functions app to use Azure AD login
   https://docs.microsoft.com/en-us/azure/app-service/configure-authentication-provider-aad

2. Application Monitoring for Azure App Service
   https://docs.microsoft.com/en-us/azure/azure-monitor/app/azure-web-apps?tabs=net

   https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview

3. Back up your app in Azure
   https://docs.microsoft.com/en-us/azure/app-service/manage-backup

4. Restore an app in Azure
   https://docs.microsoft.com/en-us/azure/app-service/web-sites-restore

5. Map an existing custom DNS name to Azure App Service
   https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain?tabs=cname

6. Create a web app and deploy code from GitHub using powershell script
   https://docs.microsoft.com/en-us/azure/app-service/scripts/powershell-deploy-github?toc=/powershell/module/toc.json

7. Create an App Service app and deploy files with FTP using Azure CLI
   https://docs.microsoft.com/en-us/azure/app-service/scripts/cli-deploy-ftp?toc=/cli/azure/toc.json
[1/9 11:05] Rk 2 Rama Krishna: Lab:

1. Enable Azure Active Directory authentication to webapp
2. Take a backup of webapp and restore it
3. Enable monitoring using application insights for webapp 
4. Look at the application monitoring metric on application insights
5. Create a webapp and host sample ASP.NET app using powershell
6. Create a webapp and host sample app using Azure CLI
[2/9 11:11] Rk 2 Rama Krishna: 1. Azure Loadbalancer
   https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview

2. Type of loadbalancers- Ineternal/Public loadbalancer
   https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview

3. Azure Load Balancer SKUs comparision (Basic/Standard)
   https://docs.microsoft.com/en-us/azure/load-balancer/skus

4. Create a public load balancer to load balance VMs
   https://docs.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard

5. Layers of OSI Model
   https://www.geeksforgeeks.org/layers-of-osi-model/

6. Azure Virtual Machine Scale set
   https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/overview
[2/9 11:14] Rk 2 Rama Krishna: Lab:

1. Create a basic loadbalancer
2. Create two VM's in same VNET and same AVSet
3. Enable web server for the VM's
4. Add this two VMs as back-end pool of loadbalancer
5. Create healthprobe and loadbalancing rules
6. Test the traffic routing using loadbalancer front end ip
[3/9 11:20] Rk 2 Rama Krishna: Load Balancing concepts:


1. Loadbalancer - Public LB & Internal(private) LB
 		  SKU - Basic & Standard
		  OSI Layer 4 (TCP & UDP)
		  BackendPool: VM & VMSS
		  Health Probe and LB Rules

2. Application Gateway

   		  OSI Layer 7 
 		  BackendPool: VM's, VMSS, Webapp & IP or FQDN
		  Listerner
		  WAF (Web application firewall)
		  Route the traffic on Path Basis
		  Multisite hosting
		  SSL Offloading (Https -> Http)

3. Traffic Manager
                 DNS Based LB
[3/9 11:24] Rk 2 Rama Krishna: 1. What is Azure Application Gateway?
   https://docs.microsoft.com/en-us/azure/application-gateway/overview

2. Direct web traffic with Azure Application Gateway - Azure portal
   https://docs.microsoft.com/en-us/azure/application-gateway/quick-create-portal

3. Direct web traffic with Azure Application Gateway using Azure PowerShell
   https://docs.microsoft.com/en-us/azure/application-gateway/quick-create-powershell

4. Direct web traffic with Azure Application Gateway - Azure CLI
   https://docs.microsoft.com/en-us/azure/application-gateway/quick-create-cli

5. What is Traffic Manager?
   https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview

6. Create a Traffic Manager profile using the Azure portal
   https://docs.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile

7. Create a Traffic Manager profile for a highly available web application using Azure PowerShell
   https://docs.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile-powershell

8. Create a Traffic Manager profile for a highly available web application using Azure CLI
    https://docs.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile-cli

In[3/9 11:25] Rk 2 Rama Krishna: Lab:

1. Create Appication Gateway
2. Add App service or VM's as a backendpool
3. Test the traffic routing
4. Create Traffic Manager
5. Create endpoints on priority condition
6. test the traffic routing

# Benifits

1.High Availability,Fault Tolerance and Disaster Recovery.

-   Availability Zones
-   Availabilty sets
-   Virtual Machine Scale Sets (VMSS)

    2.Scalability and Elasticity.

    3.Business Agility.

    4.Economies of Scale.

    5.Capital Expenditure(CapEx) and Operational Expenditure (OpEx).

    6.The Consumption-Based.

On-premises
Iaas (Infrastructure as a service) System Managers
Paas (Platform as a service) Developers and Deployers
Saas (Sofware as a service) Business User

## cloud types

1>Public Cloud

2>Private Cloud

3>Gov Cloud

# Interview Questions

<https://www.dotnettricks.com/learn/azure/top-20-azure-administrator-interview-questions-answers>

<https://www.dotnettricks.com/learn/azure/top-20-azure-developer-interview-questions-answers>

<https://technicalinfobybinit.blogspot.com/2021/07/azure-administrator-interview-questions.html>

<https://www.sqlshack.com/microsoft-azure-administrator-scenario-related-interview-questions-and-answers/>

<https://www.edureka.co/blog/interview-questions/azure-interview-questions/>

<https://www.infosectrain.com/blog/top-20-azure-administrator-interview-questions/>

<https://www.interviewbit.com/azure-interview-questions/>

<https://intellipaat.com/blog/interview-question/microsoft-azure-interview-questions/>

<https://www.simplilearn.com/tutorials/azure-tutorial/azure-interview-questions?source=sl_frs_nav_playlist_video_clicked>

<https://www.guru99.com/azure-interview-questions-answers.html>

There are quota limits on each subscription that can impact VM creation. By default, you cannot have more than 20 virtual cores across all VMs within a region. You can either split up VMs across regions or file an online request to increase your limits.

The temporary disk is not persistent. You should only write data to this disk that you are willing to lose at any time.
