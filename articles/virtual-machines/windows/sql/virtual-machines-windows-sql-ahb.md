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
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576907"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual SQL no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos de SQL - **Microsoft.SqlVirtualMachine**. Existem dois modelos de licenciamento para uma máquina virtual (VM) que hospeda o SQL Server - pagamento por uso e traz sua própria licença (BYOL). E agora, usando o Powershell ou o CLI do Azure, você pode modificar o modelo de licenciamento usado pela sua VM SQL. 

O modelo **Pagamento por uso** significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.

O **Bring-your-própria licença** modelo também é conhecido como o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que você use sua própria licença do SQL Server com uma VM executando o SQL Server. Para obter mais informações sobre preços, consulte [Guia de precificação do SQL VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença incorre em **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custo adicional** (na verdade, ativar o AHB reduz o custo) e é **efetivo imediatamente**. 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>Registrar herdados VM do SQL com o novo provedor de recursos
A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo novo provedor de recursos de VM de SQL (Microsoft.SqlVirtualMachine). No momento, para poder alternar seu modelo de licenciamento, você primeiro precisará registrar o novo provedor em sua assinatura e, em seguida, registrar sua VM herdada com o novo provedor de recursos de VM do SQL. 

  >[!IMPORTANT]
  > Se você eliminar seu recurso de VM do SQL, voltará para a configuração de licença codificada da imagem. 

### <a name="powershell"></a>Powershell

O código a seguir registra primeiro o novo provedor de recursos SQL com sua assinatura e, em seguida, registra sua VM SQL legada com o novo provedor de recursos. 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portal
Você também pode registrar o novo provedor de recursos de VM do SQL usando o portal. Para então, siga estas etapas:
1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  

  ![Selecione sua assinatura](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. Na folha **Assinaturas**, navegue para **Provedor de Recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

  ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Usar o Powershell 
Você pode usar o Powershell para alterar seu modelo de licenciamento.  Certifique-se de que sua VM SQL já tenha sido registrada com o novo provedor de recursos SQL antes de alternar o modelo de licenciamento. 

Este snippet de código alterna seu modelo de licença de pagamento por uso para BYOL (ou usando o Benefício Híbrido do Azure): 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Este snippet de código alterna seu modelo BYOL para pagamento por uso:
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se você tentar executar esses comandos antes de registrar sua VM do SQL com o novo provedor, poderá encontrar este erro: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Se vir este erro, [registre sua VM do SQL com o novo provedor de recursos](#register-legacy-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Usar a CLI do Azure
Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  Certifique-se de que sua VM SQL já tenha sido registrada com o novo provedor de recursos SQL antes de alternar o modelo de licenciamento. 

Este snippet de código alterna seu modelo de licença de pagamento por uso para BYOL (ou usando o Benefício Híbrido do Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Este snippet de código alterna seu modelo BYOL para pagamento por uso: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se você tentar executar esses comandos antes de registrar sua VM do SQL com o novo provedor, poderá encontrar este erro: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Se vir este erro, [registre sua VM do SQL com o novo provedor de recursos](#register-legacy-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Licenciamento do modo de exibição atual 

O snippet de código a seguir permite que você visualize seu modelo de licenciamento atual para sua VM do SQL. 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server em uma orientação de preços de VM do windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


