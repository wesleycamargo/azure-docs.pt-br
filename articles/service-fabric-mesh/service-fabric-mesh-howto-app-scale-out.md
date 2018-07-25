---
title: Serviços de escala em um aplicativo da Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como dimensionar serviços dentro de um aplicativo em execução na Malha do Service Fabric usando a CLI do Azure de forma independente.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089736"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Dimensionar serviços dentro de um aplicativo em execução na Malha do Service Fabric

Este artigo mostra como dimensionar os microsserviços dentro de um aplicativo de forma independente. Neste exemplo, o aplicativo Visual Objects consiste de dois microsserviços; `web` e `worker`. 

O serviço `web` é um aplicativo ASP.NET Core com uma página da web que mostra triângulos no navegador. O navegador exibe um triângulo para cada instância do serviço `worker`. 

O serviço `worker` move o triângulo em um intervalo predefinido no espaço e envia o local do triângulo para serviço `web`. Ele usa o DNS para resolver o endereço do serviço `web`.

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

## <a name="deploy-the-application-with-one-worker-service"></a>Implante o aplicativo com serviço de trabalhador
Criar seu aplicativo no grupo de recursos usando o comando `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
O comando anterior implanta um aplicativo Linux usando o [modelo mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Se você desejar implantar um aplicativo do Windows, use o [modelo mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Em alguns minutos, seu comando deve retornar com:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Abra o aplicativo
Depois que o aplicativo é implantado com êxito, obtenha o endereço IP público para o ponto de extremidade de serviço e abra-o em um navegador. Ele exibe uma página da web com um triângulo percorrendo o espaço.

O comando de implantação retorna o endereço IP público do ponto de extremidade de serviço. Opcionalmente, você também pode consultar o recurso de rede para localizar o endereço IP público do ponto de extremidade de serviço. 
 
O nome do recurso de rede para este aplicativo é `visualObjectsNetwork`, buscar informações sobre ele usando o comando a seguir. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Serviço `worker` de escala

Escala de serviço `worker` para três instâncias usando o comando a seguir. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
O comando anterior implanta um Linux usando o [modelo mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Se você desejar implantar um aplicativo do Windows, use o [modelo mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Depois que o aplicativo é implantado com êxito, o navegador deve estar exibindo uma página da web com três triângulos percorrendo o espaço.

## <a name="delete-the-resources"></a>Excluir os recursos

Para conservar os recursos limitados atribuídos no programa de visualização, exclua os recursos com frequência. Para excluir os recursos relacionados a este exemplo, exclua o grupo de recursos no qual eles foram implantados.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas
- Exiba o aplicativo de exemplo de Visual Objects no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).