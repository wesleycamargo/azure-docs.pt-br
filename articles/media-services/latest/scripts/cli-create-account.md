---
title: Criar uma conta dos Serviços de Mídia do Azure – CLI do Azure | Microsoft Docs
description: Use o script de CLI do Azure para criar uma conta dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 7e668852346de1b48bd34658dea001435ba8a625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842193"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Exemplo de CLI: Criar uma conta de Serviços de Mídia do Azure

O script de CLI do Azure neste tópico mostra como criar uma conta dos Serviços de Mídia do Azure. 

A conta de Serviços de Mídia e todas as contas de armazenamento associadas devem estar na mesma assinatura do Azure. É recomendável usar contas de armazenamento na mesma localização que a conta de Serviços de Mídia.

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Cria uma conta dos Serviços de Mídia. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Cria uma entidade de serviço com a senha e configura o acesso a uma conta dos Serviços de Mídia do Azure. 
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
