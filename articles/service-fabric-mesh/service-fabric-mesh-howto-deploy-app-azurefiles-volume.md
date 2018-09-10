---
title: Armazenar estado em um aplicativo de Malha do Azure Service Fabric montando um volume baseado em Arquivos do Azure dentro do contêiner | Microsoft Docs
description: Saiba como armazenar estado em um aplicativo de Malha do Azure Service Fabric montando um volume baseado em Arquivos do Azure dentro do contêiner usando a CLI do Azure.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037840"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Armazenar estado em um aplicativo de Malha do Azure Service Fabric montando um volume baseado em Arquivos do Azure dentro do contêiner

Este artigo mostra como armazenar o estado nos arquivos do Azure ao montar um volume dentro do contêiner de um aplicativo de Malha do Service Fabric. Neste exemplo, o aplicativo de Contador tem um serviço ASP.NET Core com uma página da web que mostra o valor do contador em um navegador. 

O `counterService` periodicamente lê um valor do contador de um arquivo, incrementa-o e o grava novamente para o arquivo. O arquivo é armazenado em uma pasta que está montada no volume de apoio de compartilhamento de arquivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir essa tarefa. Para usar a CLI do Azure com este artigo, certifique-se de que `az --version` retorna pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão da CLI de Malha do Azure Service Fabric, seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

Criar um compartilhamento de arquivos do Azure seguindo essas [instruções](/azure/storage/files/storage-how-to-create-file-share). O nome da conta de armazenamento, chave de conta de armazenamento e o nome do compartilhamento de arquivos são referenciados como `<storageAccountName>`, `<storageAccountKey>`, e `<fileShareName>` nas instruções a seguir. Esses valores estão disponíveis no portal do Azure:
* <storageAccountName> - Em **Contas de Armazenamento**, é o nome da conta de armazenamento que você usou quando criou o compartilhamento de arquivos.
* <storageAccountKey> - Selecione a conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Chaves de acesso** e use o valor em **key1**.
* <fileShareName> - Selecione a conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Arquivos**. O nome a usar é o nome do compartilhamento de arquivos que você acabou de criar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para implantar o aplicativo. O comando a seguir cria um grupo de recursos nomeado `myResourceGroup` em um local no leste dos Estados Unidos.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Implantar o modelo

Crie o aplicativo e os recursos relacionados usando o comando a seguir e forneça os valores para `storageAccountName`, `storageAccountKey` e `fileShareName` da etapa anterior [Criar um compartilhamento de arquivos](#create-a-file-share).

O parâmetro `storageAccountKey` no modelo é uma cadeia de caracteres segura. Ele não será exibido no status de implantação e comandos `az mesh service show`. Certifique-se de que ele está corretamente especificado no comando a seguir.

O comando a seguir implementa um aplicativo Linux usando o modelo [mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Para implantar um aplicativo do Windows, use o modelo [mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Esteja ciente de que imagens de contêiner maiores podem demorar mais para serem implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Em alguns minutos, o comando deverá retornar com `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Abra o aplicativo

O comando de implantação retornará o endereço IP público do ponto de extremidade de serviço. Depois que o aplicativo for implantado com êxito, obtenha o endereço IP público do ponto de extremidade de serviço e abra-o em um navegador. Ele exibirá uma página da Web com o valor do contador sendo atualizado a cada segundo.

O nome do recurso de rede para esse aplicativo é `counterAppNetwork`. É possível visualizar informações sobre o aplicativo como a descrição, o local, grupo de recursos, etc., usando o comando a seguir:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verifique se o aplicativo é capaz de usar o volume

O aplicativo cria um arquivo chamado `counter.txt` no arquivo de compartilhamento dentro da pasta `counter/counterService`. O conteúdo desse arquivo é o valor do contador que está sendo exibido na página da web.

O arquivo pode ser baixado usando qualquer ferramenta que permita a navegação em um compartilhamento de arquivos dos Arquivos do Azure como o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Excluir os recursos

Exclua com frequência os recursos que não estão mais sendo utilizados no Azure. Para excluir os recursos relacionados a esse exemplo, exclua o grupo de recursos no qual foram implantados (que exclui tudo associado ao grupo de recursos) com o comando a seguir:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Exibir o aplicativo de exemplo do volume de arquivos do Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).