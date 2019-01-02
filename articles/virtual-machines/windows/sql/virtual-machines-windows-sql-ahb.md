---
title: Como alterar o modelo de licenciamento de uma VM do SQL no Azure | Microsoft Docs
description: Saiba como alternar o licenciamento de uma máquina virtual SQL no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336978"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos de SQL - **Microsoft.SqlVirtualMachine**. Existem dois modelos de licenciamento para uma máquina virtual (VM) que hospeda o SQL Server - pagamento por uso e traz sua própria licença (BYOL). E agora, usando o PowerShell ou a CLI do Azure, você pode modificar o modelo de licenciamento usado pela sua VM SQL. 

O modelo **Pagamento por uso** significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.

O **Bring-your-própria licença** modelo também é conhecido como o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que você use sua própria licença do SQL Server com uma VM executando o SQL Server. Para obter mais informações sobre preços, consulte [Guia de precificação do SQL VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença incorre em **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custo adicional** (na verdade, ativar o AHB reduz o custo) e é **efetivo imediatamente**. 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrar VM do SQL existente com o novo provedor de recursos
A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo novo provedor de recursos de VM de SQL (Microsoft.SqlVirtualMachine). No momento, para poder alternar seu modelo de licenciamento, você primeiro precisará registrar o novo provedor em sua assinatura e, em seguida, registrar sua VM existente com o novo provedor de recursos da VM SQL. Para utilizar o provedor de recursos da VM SQL, você também precisará instalar a extensão de IaaS do SQL. Isso permitirá que você registre uma VM que foi implantada com um VHD. Para saber mais, confira [Extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
  > Se você eliminar seu recurso de VM do SQL, voltará para a configuração de licença codificada da imagem. 

### <a name="powershell"></a>PowerShell


O snippet de código a seguir conectará você ao Azure e verificará qual ID de assinatura você está usando. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

O snippet de código a seguir registra primeiro o novo provedor de recursos SQL de sua assinatura e, em seguida, registra sua VM SQL existente com o novo provedor de recursos. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portal
Você também pode registrar o novo provedor de recursos de VM do SQL usando o portal. Para então, siga estas etapas:
1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Na folha **Assinaturas**, navegue para **Provedor de Recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

  ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Usar o PowerShell 
Você pode usar o PowerShell para alterar seu modelo de licenciamento.  Certifique-se de que sua VM SQL já tenha sido registrada com o novo provedor de recursos SQL antes de alternar o modelo de licenciamento. 

O snippet de código a seguir alterna seu modelo de licença de pagamento por uso para BYOL (ou usando o Benefício Híbrido do Azure): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

O snippet de código a seguir alterna seu modelo de BYOL para pagamento por uso:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se tentar executar esses comandos antes de registrar a VM SQL no novo provedor, você poderá encontrar esse erro: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Se você vir esse erro, [registre sua VM SQL no novo provedor de recursos](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Usar a CLI do Azure
Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  Certifique-se de que sua VM SQL já tenha sido registrada com o novo provedor de recursos SQL antes de alternar o modelo de licenciamento. 

O snippet de código a seguir alterna seu modelo de licença de pagamento por uso para BYOL (ou usando o Benefício Híbrido do Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

O snippet de código a seguir alterna seu modelo de BYOL para pagamento por uso: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se tentar executar esses comandos antes de registrar a VM SQL no novo provedor, você poderá encontrar esse erro: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Se você vir esse erro, [registre sua VM SQL no novo provedor de recursos](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Licenciamento do modo de exibição atual 

O snippet de código a seguir permite que você visualize seu modelo de licenciamento atual para sua VM do SQL. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


