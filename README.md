# New-IcoAzVM
## Introduction  
This Cmdlet creates one or more virtual machines (VM) based on the ICONICS Suite offers on Azure Marketplace. Supported ICONICS Suite versions include **10.96.1**, **10.96.2** and **10.97**.  

This Cmdlet can be invoked with and without parameters. If no or insufficient parameters are provided when the Cmdlet is called, it will ask you a series of questions to guide you through the process.  

The Cmdlet will guide you through the process with a series of questions to collect the necessary parameter values as outlined in the pre-requisites. It will then allow you to confirm the collected values before you commit to creating the necessary resources.  

When you deploy ICONICS software, Microsoft can identify the installation of ICONICS software with the deployed Azure resources. Microsoft can correlate these resources used to support the software. Microsoft collects this information to provide the best experiences with their products and to operate their business. The data is collected and governed by Microsoft's privacy policies, located at https://www.microsoft.com/trustcenter.  

This Cmdlet will create the following resources as necessary:  
- Resource Group  
- Network resources (IP, subnet, NSG, NIC, VNet)  
- Virtual Machine

## Pre-requisites  
Before running this Cmdlet, ensure you have the following information:  
- **Subscription name** where resources should be created in.  
- **Resource Group name** where resources should be created in.  
    - If the given Resource Group name doesn't exist in the subscription, a new one will be created.  
- **Azure region** where resources should be created in.  
    - If an existing Resource Group is re-used, then the Azure region of the Resource Group will be applied.  
    - List of Azure regions can be found here: https://azure.microsoft.com/en-us/global-infrastructure/geographies/
    - Enter Azure regions in lowercase and without spaces
- **Number of VMs** to provision.  
- **Azure VM Size name**
- **SSD option**
    - If SSD option is chosen, VMs will use Premium SSD for its OS disk. Otherwise, Standard HDD is used.
- **Prefix name** of the virtual machines.  
    - The machine name of the VM will be generated by appending an index number to the Prefix name.  
    - The index number appended will depend on the existence of VMs with the same Prefix name.  
    - If there are VMs in the Resource Group with the same Prefix name, then the index will be the count of VMs with the same Prefix name + 1. Otherwise, it will start at 1.  
- **ICONICS Suite version**
- **User Name** of the initial administrator account.  
- **Password** that meets the following requirements:  
    - At least 12 characters long.
    - Have one or more lower case alphabets.  
    - Have one or more upper case alphabets.  
    - Have one or more numbers.  
    - Have one or more symbol characters. 
- If a new resource group is being created, you will be asked if you want to allow:  
    - **HTTP** inbound using TCP port 80  
    - **ICONICS FrameWorX** inbound using TCP port 8778  
    
    *Note that this only opens the ports at the Azure NSG level. The respective ports still need to be allowed in the VM's Windows Firewall.*  

## Using the Cmdlet  
This Cmdlet can be launched from:  
- PowerShell 7 with the Azure module
- Azure portal cloud shell  

Once in the directory where this Cmdlet is saved, invoke it by typing: **./New-IcoAzVM.ps1**  

Optionally, parameters can be passed in with the following parameter names:
- **SubscriptionName**
- **ResourceGroupName**
- **Location** *(eastus,westeurope,...)*
- **VMCount** *(1,2,3,...)*
- **VMSize** *(B2s, B2ms, D3_v2,...)*
- **UseSSD** *(Y/N)*
- **VMPrefix**
- **ICONICSversion** *(10.96.1, 10.96.2, 10.97)*
- **Username**
- **Password** *(min. 12 characters, one upper, one lower, one number, one symbol)*
- **AllowHTTP** *(Y/N)*
- **AllowFWX** *(Y/N)*
- **Confirm** *(Y/N)*  
    *This parameter instructs the cmdlet to create the resources without requiring additional confirmation*

For any parameter that is not defined when triggering the Cmdlet, prompts will show up to guide you through the rest.  

## Known issues  
1. If the list of existing VMs with the same Prefix name have out of order index numbers, this script will fail.  
        
    i.e.: if your chosen Prefix name is "TestVM" and there is "TestVM-1" and "TestVM-3" in your resource group, this script will count the 2 VMs and use 3 as the next index number, resulting in an attempt to create a VM named "TestVM-3", and failing.

## Examples
    ./New-IcoAzVM.ps1 -SubscriptionName MySubscription -ResourceGroupName MyResourceGrp -Location eastus -VMCount 2 -VMSize B4ms -UseSSD N -VMPrefix IcoVM -ICONICSversion 10.97 -Username iconicsadmin -AllowHTTP Y -AllowFWX Y
The example above would prompt for the *iconicsadmin* password and then create the following:
1. Set the subscription to *MySubscription*
1. Create *MyResourceGrp* in the *eastus* region if it doesn't exist, otherwise use the existing resource group
1. If a new resource group is created, also create the network components like subnet, VNet and NSG. The NSG will have *HTTP (TCP port 80)* and *FrameWorX (TCP port 8778)* allowed for inbound.
1. Create 2 ICONICS Suite *10.97* VMs with the *B4ms* size using *Standard HDD* OS disk.
1. If a new resource group is created, VM name will be *IcoVM-1* and *IcoVM-2*.  
        
    If resource group already exist and there is already a VM with prefix of *IcoVM*, then the VM name will be *IcoVM-2* and *IcoVM-3*.  
1. Set the initial admin of the VMs to be *iconicsadmin* with the password entered earlier.