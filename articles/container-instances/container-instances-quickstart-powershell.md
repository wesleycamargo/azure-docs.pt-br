---
title: Início Rápido – Criar o primeiro contêiner de Instâncias de Contêiner do Azure com o PowerShell
description: Neste guia de início rápido, você usa o Azure PowerShell para implantar um contêiner do Windows em Instâncias de Contêiner do Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453559"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Início Rápido: Criar o primeiro contêiner nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam criar e gerenciar contêineres do Docker no Azure, sem a necessidade de provisionar máquinas virtuais ou adotar um serviço de nível superior. Neste início rápido, você cria um contêiner do Windows no Azure e o expõe à Internet com um FQDN (nome de domínio totalmente qualificado). Essa operação é concluída com um único comando. Em apenas alguns minutos, você consegue ver o aplicativo em execução no seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][qs-powershell-01]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.5 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contêiner

Você pode criar um contêiner fornecendo um nome, uma imagem do Docker e um grupo de recursos do Azure para o cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Outra opção é expor o contêiner à Internet com um rótulo de nome DNS.

Execute o seguinte comando para iniciar um contêiner do Nano Server executando os Serviços de Informações da Internet (IIS). O valor `-DnsNameLabel` deve ser exclusivo dentro da região do Azure na qual você criar a instância, portanto, talvez seja preciso modificar esse valor para garantir a exclusividade.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Em alguns segundos, você deverá obter uma resposta à solicitação. Inicialmente, o contêiner fica no estado **Criando**, mas ele deve começar em alguns segundos. Você pode verificar o status de implantação usando o cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

O estado de provisionamento, o FQDN (nome de domínio totalmente qualificado) e o endereço IP do contêiner aparecem na saída do cmdlet:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Assim que o contêiner **ProvisioningState** mudar para `Succeeded`, navegue até seu `Fqdn` em seu navegador:

![IIS implantado usando as Instâncias de Contêiner do Azure exibido no navegador][qs-powershell-01]

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir o contêiner, remova-o usando o cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de Contêiner do Azure com base em uma imagem em no registro do Hub do Docker público. Se você quiser tentar criar uma imagem de contêiner sozinho e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
