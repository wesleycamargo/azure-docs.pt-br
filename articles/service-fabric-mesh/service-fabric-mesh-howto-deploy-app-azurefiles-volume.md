---
title: Estado de armazenamento ao montar o volume de arquivos do Azure com base em dentro do contêiner no aplicativo de Malha do Service Fabric | Microsoft Docs
description: Aprenda como armazenar estado ao montar o volume de arquivos do Azure com base em dentro do contêiner no aplicativo de Malha do Service Fabric usando a CLI do Azure.
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
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090625"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Armazene estado ao montar o volume de arquivos do Azure com base no aplicativo de Malha do Service Fabric

Este artigo mostra como armazenar o estado nos arquivos do Azure ao montar um volume dentro do contêiner de um aplicativo de Malha do Service Fabric. Neste exemplo, o aplicativo de contador tem um serviço ASP.NET Core com uma página da web que mostra o valor do contador em um navegador. 

O `counterService` periodicamente lê um valor do contador de um arquivo, incrementa-o e o grava novamente para o arquivo. O arquivo é armazenado em uma pasta que está montada no volume de apoio de compartilhamento de arquivos do Azure. 

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir essa tarefa. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Criar o compartilhamento de arquivos 
Criar um compartilhamento de arquivos do Azure seguindo essas [instruções](/azure/storage/files/storage-how-to-create-file-share). O nome da conta de armazenamento, chave de conta de armazenamento e o nome do compartilhamento de arquivos são referenciados como `<storageAccountName>`, `<storageAccountKey>`, e `<fileShareName>` nas instruções a seguir.

## <a name="create-resource-group"></a>Criar grupo de recursos
Crie um grupo de recursos para implantar o aplicativo. Você pode usar um grupo de recursos existente e ignorar esta etapa. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Implantar o modelo

Criar o aplicativo e os recursos relacionados usando o comando a seguir e forneça os valores para `storageAccountName`, `storageAccountKey` e `fileShareName` da etapa anterior.

O parâmetro `storageAccountKey` no modelo é um `securestring`. Ele não será exibido no status de implantação e comandos `az mesh service show`. Certifique-se de que ele está corretamente especificado no comando a seguir.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

O comando anterior implanta um aplicativo Linux usando o [modelo mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Se você desejar implantar um aplicativo do Windows, use o [modelo mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Em alguns minutos, seu comando deve retornar com:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Abra o aplicativo
Depois que o aplicativo é implantado com êxito, obtenha o endereço IP público para o ponto de extremidade de serviço e abra-o em um navegador. Ele exibe uma página da web com o valor do contador que está sendo atualizado a cada segundo.

O comando de implantação retorna o endereço IP público do ponto de extremidade de serviço. Opcionalmente, você também pode consultar o recurso de rede para localizar o endereço IP público do ponto de extremidade de serviço. 
 
O nome do recurso de rede para este aplicativo é `counterAppNetwork`, buscar informações sobre ele usando o comando a seguir. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verifique se o aplicativo é capaz de usar o volume
O aplicativo cria um arquivo chamado `counter.txt` no arquivo de compartilhamento dentro da pasta `counter/counterService`. O conteúdo desse arquivo é o valor do contador que está sendo exibido na página da web.

O arquivo pode ser baixado usando qualquer ferramenta que permite a navegação de um compartilhamento de arquivos de arquivos do Azure. O [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) é um exemplo desse tipo de ferramenta.

## <a name="delete-the-resources"></a>Excluir os recursos

Para conservar os recursos limitados atribuídos no programa de visualização, exclua os recursos com frequência. Para excluir os recursos relacionados a este exemplo, exclua o grupo de recursos no qual eles foram implantados.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

- Exibir o aplicativo de exemplo do volume de arquivos do Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).
