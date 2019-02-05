---
title: Início Rápido – criar um registro particular do Docker no Azure – PowerShell
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker no Azure com o PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299736"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Início Rápido: Criar um registro de contêiner privado do Docker usando o Azure PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner do Docker privado e gerenciado para gerar, armazenar e fornecer imagens de contêiner de Docker. Neste início rápido, você aprende a criar um registro de contêiner do Azure usando o PowerShell. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer o módulo Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para determinar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

Você também deve ter o Docker instalado localmente. O docker fornece pacotes para sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Como o Azure Cloud Shell não inclui todos os componentes de Docker necessários (o daemon `dockerd`), você não pode usar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon em sua assinatura do Azure com o comando [Connect-AzureRmAccount][Connect-AzureRmAccount] e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Depois que você estiver autenticado com o Azure, crie um grupo de recursos com [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Um grupo de recursos é um contêiner lógico no qual você implanta e gerenciar os recursos do Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar um registro de contêiner

Em seguida, crie um registro de contêiner em seu novo grupo de recursos com o comando [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. O exemplo a seguir cria um registro chamado "myContainerRegistry007". Substitua *myContainerRegistry007* no comando a seguir e execute-o para criar o registro:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [SKUs de registro de contêiner][container-registry-skus].

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull nas imagens de contêiner, você deverá fazer logon no seu registro. Em cenários de produção, você deve usar uma identidade individual ou a entidade de serviço para acesso de registro de contêiner, porém, para manter este início rápido breve, habilite o usuário administrador no seu registro com o comando [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential]:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Em seguida, execute [docker login][docker-login] para fazer logon:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando retorna `Login Succeeded` na conclusão.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com os recursos criados neste guia de início rápido, use o comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com o Azure PowerShell, efetuou push de uma imagem de contêiner e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md