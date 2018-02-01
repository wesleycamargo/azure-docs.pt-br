---
title: "Guia de início rápido – criar um registro particular do Docker no Azure com o PowerShell"
description: "Aprenda rapidamente a criar um registro de contêiner particular do Docker com o PowerShell."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c7d74395b1c8b386ce190906aa5b63b48c1bb1bf
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Criar um Registro de Contêiner do Azure usando o PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando o PowerShell.

Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Crie uma instância do ACR usando o comando [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Primeiro, use o comando [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) para obter as credenciais de administrador da instância do ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Depois, use o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) para fazer logon na instância do ACR.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

O comando retorna uma mensagem de 'Logon bem-sucedido' assim que ele for concluído.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se necessário, execute o seguinte comando para efetuar pull de uma imagem pré-criada do Hub do Docker.

```bash
docker pull microsoft/aci-helloworld
```

A imagem precisa ser marcada com o nome do servidor de logon do ACR. Execute o comando [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) para retornar o nome do servidor de logon da instância do ACR.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Marque a imagem usando o comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Substitua o *acrLoginServer* pelo nome do servidor de logon da sua instância do ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por fim, use [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar as imagens por push para a instância do ACR. Substitua o *acrLoginServer* pelo nome do servidor de logon da sua instância do ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, a instância do ACR e todas as imagens de contêiner.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você ciou um Registro de Contêiner do Azure com a CLI do Azure. Se você quiser usar o Registro de Contêiner do Azure com Instâncias de Contêiner do Azure, prossiga para o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)