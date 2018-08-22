---
title: Serviços de escala em um aplicativo da Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como dimensionar serviços dentro de um aplicativo em execução na Malha do Service Fabric usando a CLI do Azure de forma independente.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037899"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Dimensionar serviços dentro de um aplicativo em execução na Malha do Service Fabric

Este artigo mostra como dimensionar os microsserviços dentro de um aplicativo de forma independente. Neste exemplo, o aplicativo Visual Objects consiste em dois microsserviços: `web` e `worker`.

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

Crie um grupo de recursos para implantar o aplicativo.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Implante o aplicativo com serviço de trabalhador

Criar seu aplicativo no grupo de recursos usando o comando `deployment create`.

O exemplo a seguir implementa um aplicativo Linux usando o modelo [ mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Para implantar um aplicativo do Windows, use o modelo [[mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Em alguns minutos, o comando deverá retornar com:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Abra o aplicativo

O comando de implantação retornará o endereço IP público do ponto de extremidade de serviço. Depois que o aplicativo for implantado com êxito, obtenha o endereço IP público do ponto de extremidade de serviço e abra-o em um navegador. Ele exibirá uma página da Web com um triângulo móvel.

O nome do recurso de rede para esse aplicativo é `visualObjectsNetwork`. É possível visualizar informações sobre o aplicativo como a descrição, o local, grupo de recursos, etc., usando o comando a seguir:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Serviço `worker` de escala

Escala de serviço `worker` para três instâncias usando o comando a seguir. O exemplo a seguir implementa um aplicativo Linux usando o modelo [ mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Para implantar um aplicativo do Windows, use o modelo [mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Esteja ciente de que imagens de contêiner maiores podem demorar mais para serem implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

Depois que o aplicativo for implantado com êxito, o navegador deverá exibir uma página da Web com três triângulos móveis.

## <a name="delete-the-resources"></a>Excluir os recursos

Exclua com frequência os recursos que não estão mais sendo utilizados no Azure. Para excluir os recursos relacionados a esse exemplo, exclua o grupo de recursos no qual foram implantados (que exclui tudo associado ao grupo de recursos) com o comando a seguir:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Exiba o aplicativo de exemplo de Visual Objects no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).