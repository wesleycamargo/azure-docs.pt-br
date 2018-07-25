---
title: Implantar um aplicativo na Malha do Azure Service Fabric usando um modelo | Microsoft Docs
description: Saiba como implantar um aplicativo .NET Core à Malha do Service Fabric de um modelo usando a CLI do Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceputal
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d813669f2f44fd64db669e9750e3bc064c7f916
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090319"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Implantar um aplicativo de Malha do Service Fabric para a malha de Service Fabric usando um modelo
Este artigo mostra como implantar um aplicativo .NET Core à Malha de Service Fabric usando um modelo. Quando terminar, você terá um aplicativo de votação com um front-end da Web do ASP.NET Core que salva os resultados da votação em um serviço de back-end com estado do cluster. O front-end usa o DNS para resolver o endereço do serviço de back-end.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir essa tarefa. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Criar grupo de recursos
Crie um grupo de recursos para implantar o aplicativo. Você pode usar um grupo de recursos existente e ignorar esta etapa. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Criar seu aplicativo no grupo de recursos usando o comando `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

O comando anterior implanta um aplicativo do Windows usando [modelo mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Se você desejar implantar um aplicativo do Linux, use [modelo mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Em alguns minutos, seu comando deve retornar com:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Abra o aplicativo
Depois que o aplicativo é implantado com êxito, obtenha o endereço IP público para o ponto de extremidade de serviço e abra-o em um navegador. Isso exibe A página da Web a seguir. 

![Aplicativo de votação](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Agora você pode adicionar opções de votação para o aplicativo e votar nelas ou excluir as opções de votação.

O comando de implantação retorna o endereço IP público do ponto de extremidade de serviço. Opcionalmente, você também pode consultar o recurso de rede para localizar o endereço IP público do ponto de extremidade de serviço. 

O nome do recurso de rede para este aplicativo é `VotingAppNetwork`, buscar informações sobre ele usando o comando a seguir. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Verifique os detalhes do aplicativo
Você pode verificar o status do aplicativo usando o comando `app show`. O nome do aplicativo para o aplicativo implantado é "VotingApp", então, busque seus detalhes. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Liste os aplicativos implantados
É possível usar o comando "lista de aplicativos" para obter uma lista dos aplicativos implantados em sua assinatura. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando você não precisa mais do aplicativo e ele está relacionado a recursos, exclua o grupo de recursos que os contém. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Próximas etapas
- Exiba o aplicativo de exemplo de votação no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).


