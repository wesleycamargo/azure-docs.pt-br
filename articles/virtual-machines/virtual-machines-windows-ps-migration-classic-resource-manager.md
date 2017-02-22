---
title: Migrar para o Resource Manager com o PowerShell | Microsoft Docs
description: "Este artigo apresenta a migração de recursos IaaS com suporte da plataforma do clássico ao Azure Resource Manager usando os comandos do Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e90036d97451b271451d0ba5845c788ac05d7abf
ms.openlocfilehash: 4253d60a8a12877a3c5dac073bd06d70d020ccdc


---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell
Estas etapas mostram como usar os comandos do Azure PowerShell para migrar os recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o Modelo de implantação do Azure Resource Manager. 

Se quiser, você também pode migrar recursos usando a [Interface de linha de comando do Azure (CLI do Azure)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

* Para obter informações sobre cenários de migração com suporte, confira [Migração de recursos de IaaS com suporte da plataforma do clássico ao Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md). 
* Para obter orientação e um passo a passo sobre a migração, confira [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico ao Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).
* [Examinar os erros de migração mais comuns](virtual-machines-migration-errors.md)

## <a name="step-1-plan-for-migration"></a>Etapa 1: Planejar a migração
Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

* Leia os [recursos e configurações com e sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos aguardar até que o suporte do recurso/configuração seja anunciado. Como alternativa, se isso atender às suas necessidades, remova esse recurso ou mude a configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente não ha suporte para Gateways de Aplicativo para a migração do clássico para o Resource Manager. Para migrar uma rede virtual clássica com um Gateway de Aplicativo, remova o gateway antes de executar uma operação de Confirmação para mover a rede (você pode executar a etapa de Preparação sem excluir o Gateway de Aplicativo). Depois de concluir a migração, reconecte o gateway no Azure Resource Manager. Você deve entrar em contato com o suporte se quiser migrar os gateways de ExpressRoute em casos nos quais o gateway e o circuito de ExpressRoute estão na mesma assinatura. Não é possível migrar gateways de ExpressRoute conectando-se a circuitos de ExpressRoute em outra assinatura. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway.
> 
> 

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Etapa 2: instalar a versão mais recente do Azure PowerShell
Há duas opções principais para instalar o Azure PowerShell, a [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) e o [WebPI (Web Platform Installer)](http://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Este artigo tem base no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

<br>

## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Etapa 3: Definir sua assinatura e se inscrever para a migração
Primeiro, inicie um prompt do PowerShell. Para migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Resource Manager.

Entre em sua conta para o modelo do Gerenciador de Recursos.

```powershell
    Login-AzureRmAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome da assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro: 
> 
> *BadRequest: a assinatura não está registrada para migração.* 
> 
> 

Registre-se no provedor de recursos de migração usando o comando a seguir:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se RegistrationState é `Registered` antes de continuar. 

Agora, entre em sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 4: Verificar se você tem uma quantidade suficiente de núcleos de Máquina Virtual do Azure Resource Manager na região do Azure de sua implantação atual ou VNET
Você pode usar o seguinte comando do PowerShell para verificar a quantidade atual de núcleos no Azure Resource Manager. Para saber mais sobre cotas de núcleos, veja [Limites e o Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Este exemplo verifica a disponibilidade na região **Oeste dos EUA**. Substitua o nome da região de exemplo pelo nome da sua própria região. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Etapa 5: Executar comandos para migrar os recursos de IaaS
> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tentará novamente a ação.
> 
> 

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual, ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome da implantação do serviço de nuvem. Neste exemplo, o nome do serviço é **Meu Serviço**. Substitua o nome do serviço de exemplo pelo nome de seu próprio serviço. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

* **Opção 1. Migrar as VMs para uma rede virtual criada com uma plataforma**
  
    Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:
  
    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```
  
    O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá passar para a etapa de **Preparação**:
  
    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2. Migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos**
  
    Este exemplo define o nome do grupo de recursos como **myResourceGroup**, o nome de rede virtual como **myVirtualNetwork** e o nome da sub-rede como **mySubNet**. Substitua os nomes de exemplo pelos nomes de seus próprios recursos.
  
    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```
  
    Primeiro, valide se você pode migrar o serviço de nuvem usando o seguinte comando:
  
    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```
  
    O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de Preparação:
  
    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação de Preparação ser bem-sucedida com uma das opções anteriores, consulte o estado de migração das VMs. Certifique-se de que elas estejam no estado `Prepared` .

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo pelo nome de sua própria VM.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrar máquinas virtuais em uma rede virtual
Para migrar máquinas virtuais em uma rede virtual, migre a rede. As máquinas virtuais são migradas automaticamente com a rede. Selecione a rede virtual que você deseja migrar. 

Este exemplo define o nome de rede virtual como **myVnet**. Substitua o nome de exemplo pelo nome da sua própria rede virtual. 

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.
> 
> 

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de Preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrar uma conta de armazenamento
Depois de concluir a migração das máquinas virtuais, recomendamos a migração da conta de armazenamento.

Prepare cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo pelo nome da sua própria conta de armazenamento. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Verifique a configuração da conta de armazenamento preparada usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre migração, confira [Migração de recursos de IaaS com suporte da plataforma do clássico ao Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
* Para migrar recursos de rede adicionais para o Resource Manager usando o Azure PowerShell, use etapas semelhantes com [Move-AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Move-AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx) e [Move-AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
* Para scripts de código-fonte aberto que você pode usar para migrar os recursos do Azure do clássico ao Resource Manager, confira [Ferramentas de comunidade para a migração no Azure Resource Manager](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO2-->


