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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c2f302d7b87426115df716dfba638eee0756f79
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480729"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos da VM do SQL – **Microsoft.SqlVirtualMachine**. Há dois modelos para uma máquina virtual (VM) que hospeda o SQL Server - pago conforme o uso, de licenciamento e Traga sua própria licença (BYOL). E agora, usando o PowerShell ou a CLI do Azure, você pode modificar o modelo de licenciamento usado pela sua VM do SQL Server. 

O **pré-pago** modelo (PAYG) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.

O **traga-your-própria licença** modelo (BYOL) também é conhecido como o [benefício de híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que você use sua própria licença do SQL Server com uma VM executando o SQL Server. Para obter mais informações sobre preços, confira [Guia de precificação da VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença não resulta em **nenhum tempo de inatividade**, não reinicia a VM, não gera **nenhum custo adicional** (na verdade, ativar o AHB *reduz* o custo) e **entra em vigor imediatamente**. 

  >[!NOTE]
  > - A capacidade de converter o modelo de licenciamento está disponível atualmente somente na inicialização com uma imagem de VM do SQL Server pré-paga. Se você iniciar com uma imagem Traga sua própria licença no portal, não poderá converter essa imagem para pré-paga.
  > - Os clientes de CSP podem utilizar o AHB primeiro implantando uma VM pré-paga e, em seguida, convertendo-a para Traga sua própria licença. 
  > - Atualmente, essa capacidade é habilitada apenas para instalações de nuvem pública.


## <a name="prerequisites"></a>Pré-requisitos
O uso do provedor de recursos de VM do SQL requer a extensão IaaS do SQL. Assim, para continuar utilizando o provedor de recursos de VM do SQL, você precisará do seguinte:
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Um *pré-pagas* [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com o [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalado. 


## <a name="register-sql-resource-provider-with-your-subscription"></a>Registrar o provedor de recursos do SQL em sua assinatura 

A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo novo provedor de recursos de VM de SQL (Microsoft.SqlVirtualMachine). VMs do SQL Server implantadas depois de dezembro de 2018 são automaticamente registradas com o novo provedor de recursos. No entanto, as VMs existentes que foram implantadas antes dessa data precisam ser registradas manualmente com o provedor de recursos antes que sejam capazes de mudar o seu modelo de licenciamento. 

  > [!NOTE] 
  > Se você eliminar seu recurso de VM do SQL Server, voltará para a configuração de licença codificada da imagem. 

Para registrar sua VM do SQL Server no provedor de recursos do SQL, você deverá registrar o provedor de recursos em sua assinatura. Você pode fazer isso com a CLI do Azure, PowerShell, ou com o portal do Azure. 

### <a name="with-the-azure-portal"></a>Com o portal do Azure
As etapas a seguir registrarão o provedor de recursos do SQL em sua assinatura do Azure usando o portal do Azure. 

1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Na folha **Assinaturas**, navegue para **Provedor de recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

   ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Com a CLI do Azure
O trecho de código a seguir registrará o provedor de recursos do SQL em sua assinatura do Azure. 

```cli
# Register the new SQL resource provider for your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>Com o PowerShell
O trecho de código a seguir registrará o provedor de recursos do SQL em sua assinatura do Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrar a VM do SQL Server no provedor de recursos do SQL
Depois que o provedor de recursos do SQL tiver sido registrado com sua assinatura, você pode registrar sua VM do SQL Server com o provedor de recursos. Você pode fazer isso usando a CLI do Azure e o PowerShell. 

### <a name="with-azure-cli"></a>Com a CLI do Azure

Registre-se a VM do SQL Server usando a CLI do Azure com o trecho de código a seguir: 

```cli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>Com o PowerShell

Registre-se a VM do SQL Server usando o PowerShell com o trecho de código a seguir: 
```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="change-licensing-model"></a>Alterar o modelo de licenciamento
Depois que sua VM do SQL Server tiver sido registrado com o provedor de recursos, você pode alterar o modelo de licenciamento usando o portal do Azure, CLI do Azure ou PowerShell. 


  >[!NOTE]
  >  A capacidade de converter o modelo de licenciamento está disponível atualmente somente na inicialização com uma imagem de VM do SQL Server pré-paga. Se você iniciar com uma imagem Traga sua própria licença no portal, não poderá converter essa imagem para pré-paga. 

### <a name="with-the-azure-portal"></a>Com o portal do Azure
Você pode modificar o modelo de licenciamento diretamente do portal. 

1. Navegue até sua VM do SQL Server dentro de [portal do Azure](https://portal.azure.com). 
1. Selecione **configuração do SQL Server** na **configurações** painel. 
1. Selecione **edite** na **licença do SQL Server** painel para modificar a licença. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Essa opção não está disponível para imagens traga-your-própria licença. 

### <a name="with-azure-cli"></a>Com a CLI do Azure
Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  

O trecho de código a seguir alterna o modelo pago conforme o uso de licença para BYOL (ou usando o benefício híbrido do Azure):
```azurecli
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O trecho de código a seguir alterna o modelo BYOL para pré-pago: 
```azurecli
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>Com o PowerShell 
Você pode usar o PowerShell para alterar seu modelo de licenciamento. 

O trecho de código a seguir alterna o modelo pago conforme o uso de licença para BYOL (ou usando o benefício híbrido do Azure): 
```powershell
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

O trecho de código a seguir alterna o modelo BYOL para pré-pago:
```powershell
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 


## <a name="view-current-licensing"></a>Licenciamento do modo de exibição atual 

O snippet de código a seguir permite que você exiba seu modelo de licenciamento atual para sua VM do SQL Server. 

```powershell
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
```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Use o código a seguir para verificar sua versão do Azure PowerShell:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >' no grupo de recursos '< resource-group >' não foi encontrado. A propriedade 'sqlServerLicenseType' não pode ser encontrada neste objeto. Verifique se a propriedade existe e pode ser definida.
Esse erro ocorre quando a VM do SQL Server não foi registrado com o provedor de recursos do SQL. Você precisará registrar o provedor de recursos com sua [assinatura](#register-sql-resource-provider-with-your-subscription)e, em seguida, registre sua VM do SQL Server com o SQL [provedor de recursos](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


