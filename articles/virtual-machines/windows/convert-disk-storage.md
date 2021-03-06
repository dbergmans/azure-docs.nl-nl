---
title: Converteren van Azure managed disks-opslag van standard naar premium, en vice versa | Microsoft Docs
description: Het converteren van Azure beheerde schijven van standard naar premium, en vice versa, met behulp van Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.openlocfilehash: 958f661585b38b156cf523fe00986e7594474917
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093813"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Het opslagtype van een beheerde schijf bijwerken

Azure Managed Disks biedt drie opties voor het type opslag: [Premium SSD](../windows/premium-storage.md), [Standard-SSD](../windows/disks-standard-ssd.md), en [Standard HDD](../windows/standard-storage.md). U kunt een beheerde schijf tussen opslagtypen met minimale downtime, op basis van uw prestatiebehoeften overschakelen. Schakelen tussen opslagtypen wordt niet ondersteund voor een niet-beheerde schijf. u kunt echter eenvoudig [niet-beheerde schijven converteren naar een beheerde schijf](convert-unmanaged-to-managed-disks.md).

In dit artikel laat zien hoe een beheerde schijf converteren van standard naar premium, en vice versa, met behulp van Azure PowerShell. Als u wilt installeren of upgraden van PowerShell, Zie [Azure PowerShell installeren en configureren](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="prerequisites"></a>Vereisten

* Omdat de conversie opnieuw moet opstarten van de virtuele machine (VM), moet u plannen dat de migratie van uw opslag schijven tijdens een reeds bestaande onderhoudsperiode. 
* Als u een niet-beheerde schijf eerst [converteren naar een beheerde schijf](convert-unmanaged-to-managed-disks.md) waarmee u kunt het schakelen tussen de opslagtypen. 
* De voorbeelden in dit artikel moet de Azure PowerShell-moduleversie 6.0.0 of hoger. Voer `Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-azurerm-ps). Voer [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) voor het maken van een verbinding met Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Alle beheerde schijven van een virtuele machine converteren van standard naar premium

Het volgende voorbeeld ziet hoe u wilt overschakelen van alle schijven van een virtuele machine van standaard naar premium storage. Voor het gebruik van premium-beheerde schijven, de virtuele machine moet gebruiken een [VM-grootte](sizes.md) die ondersteuning biedt voor premium-opslag. In dit voorbeeld verandert ook in een grootte die ondersteuning biedt voor premium-opslag:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Een beheerde schijf converteren van standard naar premium

Voor uw workload ontwikkelen en testen, kunt u een combinatie van standard en premium-schijven om uw kosten te beperken. De taken dit het geval is, voer een upgrade naar premium-opslag betere alleen op deze schijven waarvoor prestaties. Het volgende voorbeeld laat zien hoe om over te schakelen van één schijf van een virtuele machine van standard naar premium storage, en vice versa. Voor het gebruik van premium-beheerde schijven, de virtuele machine moet gebruiken een [VM-grootte](sizes.md) die ondersteuning biedt voor premium-opslag. In dit voorbeeld laat ook zien hoe overschakelen naar een grootte die ondersteuning biedt voor premium-opslag:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Een beheerde schijf converteren van standard HDD naar standard-SSD

Het volgende voorbeeld laat zien hoe om over te schakelen van één schijf van een virtuele machine van de standaard harde schijf naar de standard-SSD, en vice versa:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Volgende stappen

Een alleen-lezen kopie van een virtuele machine maken met behulp van een [momentopname](snapshot-copy-managed-disk.md).

