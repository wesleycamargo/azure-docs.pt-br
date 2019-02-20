---
title: Como alterar o modelo de licenciamento de uma VM do SQL Server no Azure | Microsoft Docs
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
ms.openlocfilehash: 8526716b299d26d8d70c9c5e5cdace34e188d019
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111060"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos da VM do SQL – **Microsoft.SqlVirtualMachine**. Existem dois modelos de licenciamento para uma máquina virtual (VM) que hospeda o SQL Server - pagamento por uso e traz sua própria licença (BYOL). E agora, usando o PowerShell ou a CLI do Azure, você pode modificar o modelo de licenciamento usado pela sua VM do SQL Server. 

O modelo **PAYG** (Pré-pago) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.

O modelo **BYOL** (traga sua própria licença) também é conhecido como o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) e permite que você use sua própria licença do SQL Server com uma VM que executa o SQL Server. Para obter mais informações sobre preços, confira [Guia de precificação da VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença não resulta em **nenhum tempo de inatividade**, não reinicia a VM, não gera **nenhum custo adicional** (na verdade, ativar o AHB *reduz* o custo) e **entra em vigor imediatamente**. 

  >[!NOTE]
  > - A capacidade de converter o modelo de licenciamento está disponível atualmente somente na inicialização com uma imagem de VM do SQL Server pré-paga. Se você iniciar com uma imagem Traga sua própria licença no portal, não poderá converter essa imagem para pré-paga. 
  > - Os clientes de CSP podem utilizar o AHB primeiro implantando uma VM pré-paga e, em seguida, convertendo-a para Traga sua própria licença. 


## <a name="prerequisites"></a>Pré-requisitos
O uso do provedor de recursos de VM do SQL requer a extensão IaaS do SQL. Assim, para continuar utilizando o provedor de recursos de VM do SQL, você precisará do seguinte:
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com a [extensão IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalada. 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>Registrar uma VM do SQL Server existente em um novo provedor de recursos de SQL
A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo novo provedor de recursos de VM de SQL (Microsoft.SqlVirtualMachine). VMs do SQL Server implantadas depois de dezembro de 2018 são automaticamente registradas com o novo provedor de recursos. No entanto, as VMs existentes que foram implantadas antes dessa data precisam ser registradas manualmente com o provedor de recursos antes que sejam capazes de mudar o seu modelo de licenciamento. 

  > [!NOTE] 
  > Se você eliminar seu recurso de VM do SQL, voltará para a configuração de licença codificada da imagem. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>Registrar o provedor de recursos do SQL em sua assinatura 

Para registrar sua VM do SQL Server no provedor de recursos do SQL, você deverá registrar o provedor de recursos em sua assinatura. É possível fazer isso com o PowerShell ou com o portal do Azure. 

#### <a name="using-powershell"></a>Usando o PowerShell
O trecho de código a seguir registrará o provedor de recursos do SQL em sua assinatura do Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>Usando o Portal do Azure
As etapas a seguir registrarão o provedor de recursos do SQL em sua assinatura do Azure usando o portal do Azure. 

1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Na folha **Assinaturas**, navegue para **Provedor de recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

  ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrar a VM do SQL Server no provedor de recursos do SQL
Depois que o provedor de recursos do SQL tiver sido registrado em sua assinatura, você poderá usar o PowerShell para registrar a VM do SQL Server no provedor de recursos do SQL. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>Usar o PowerShell 
Você pode usar o PowerShell para alterar seu modelo de licenciamento.  Verifique se a VM do SQL Server já foi registrada com o novo provedor de recursos SQL antes de mudar o modelo de licenciamento. 

O snippet de código a seguir alterna seu modelo de licença de pagamento por uso para BYOL (ou usando o Benefício Híbrido do Azure): 
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 

O snippet de código a seguir alterna seu modelo de BYOL para pagamento por uso:
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

  >[!NOTE]
  > Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se tentar executar esses comandos antes de registrar a VM do SQL Server no novo provedor, você poderá encontrar esse erro: `Get-AzResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Se você vir esse erro, registre sua VM do SQL Server no novo provedor de recursos. 

 

## <a name="use-azure-cli"></a>Usar a CLI do Azure
Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  Verifique se a VM do SQL Server já foi registrada com o novo provedor de recursos SQL antes de mudar o modelo de licenciamento. 

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
  >Para alternar entre licenças, você deve estar usando o novo provedor de recursos de VM do SQL. Se tentar executar esses comandos antes de registrar a VM do SQL Server no novo provedor, você poderá encontrar esse erro: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Se você vir esse erro, [registre sua VM do SQL Server no novo provedor de recursos](#register-existing-sql-server-vm-with-sql-resource-provider). 

## <a name="view-current-licensing"></a>Licenciamento do modo de exibição atual 

O snippet de código a seguir permite que você exiba seu modelo de licenciamento atual para sua VM do SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Erros conhecidos

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>A Extensão IaaS do SQL não está instalada na máquina virtual
A extensão IaaS do SQL é um pré-requisito necessário para registrar sua VM do SQL Server com o provedor de recursos de VM do SQL. Se tentar registrar a VM do SQL Server antes de instalar a extensão IaaS do SQL, você encontrará o seguinte erro:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Para resolver esse problema, instale a extensão IaaS do SQL antes de tentar registrar a VM do SQL Server. 

  > [!NOTE]
  > A instalação da extensão IaaS do SQL reiniciará o serviço do SQL Server e só deve ser realizada durante uma janela de manutenção. Para obter mais informações, confira [Instalação da extensão de IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro 'Sku'
Você pode encontrar esse erro ao tentar mudar o modelo de licenciamento de VM do SQL Server ao usar o Azure PowerShell > 4.0:

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver esse erro, ao mudar seu modelo de licenciamento, remova a marca de comentário dessas linhas no snippet de código do PowerShell mencionado anteriormente: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Use o código a seguir para verificar sua versão do Azure PowerShell:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


