---
title: Início Rápido - Criar uma conta dos Serviços de Mídia do Azure com a CLI 2.0 | Microsoft Docs
description: Execute as etapas neste início rápido para criar uma conta dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Início Rápido: Criar uma conta dos Serviços de Mídia do Azure

> [!NOTE]
> A versão mais recente dos Serviços de Mídia do Azure (2018-03-30) está em versão prévia. Esta versão também é chamada de v3. 

Não importa se você for um desenvolvedor ou um criador de conteúdo de mídia, para armazenar, criptografar, codificar, gerenciar e transmitir o conteúdo de mídia no Azure, você precisará criar uma conta dos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer a ID de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. Essa recurso de conta de armazenamento deve estar na mesma região geográfica que a conta dos Serviços de Mídia.  

Este início rápido descreve as etapas para criar uma nova conta dos Serviços de Mídia do Azure usando a CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](http://portal.azure.com) e inicie o **CloudShell** para executar comandos da CLI, conforme mostra as etapas a seguir.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Definir uma assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure

O comando a seguir cria um grupo de recursos no qual você deseja ter a conta de Armazenamento e de Serviços de Mídia. Substitua o espaço reservado *myresourcegroup* pelo nome que você deseja usar para seu grupo de recursos.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer a ID de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. 

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente blob não são permitidas como **Primárias**. Se quiser saber mais sobre as contas de armazenamento, confira [Opções de conta de Armazenamento do Azure](../../storage/common/storage-account-options.md). 

O comando a seguir cria a conta de Armazenamento que será associada à conta dos Serviços de Mídia (primária). No script a seguir, substitua o espaço reservado*storageaccountforams*. O tamanho de 'account_name' deve ser inferior a 24.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Criar uma conta de Serviços de Mídia do Azure

Veja abaixo os comandos da CLI do Azure que criam uma nova conta dos Serviços de Mídia. Você só precisa substituir os valores realçados a seguir:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo a conta dos Serviços de Mídia que você criou neste Início Rápido, exclua o grupo de recursos.

No **CloudShell**, execute o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
