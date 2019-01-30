---
title: Implantar modelos com a linha de comando no Azure Stack | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para implantar modelos do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 2b64591b9042b2c972b149427359a447f80a640e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251497"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implantar modelos no Azure Stack usando a linha de comando

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Use a linha de comando para implantar modelos do Azure Resource Manager no ambiente do Kit de desenvolvimento do Azure Stack. Modelos do Gerenciador de recursos do Azure implantam e provisionam todos os recursos para seu aplicativo em uma única operação coordenada.

## <a name="before-you-begin"></a>Antes de começar

- [Instalar e conectar](azure-stack-version-profiles-azurecli2.md) para o Azure Stack com CLI do Azure.
- Baixar os arquivos *azuredeploy. JSON* e *azuredeploy.parameters.json* do [criar modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Implantar modelo

Navegue até a pasta na qual esses arquivos foram baixados e execute o seguinte comando para implantar o modelo:

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

Esse comando implanta o modelo para o grupo de recursos **cliRG** no local padrão do Azure Stack POC.

## <a name="validate-template-deployment"></a>Validar a implantação de modelo

Para ver essa conta de armazenamento e grupo de recursos, use os seguintes comandos CLI:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como implantar modelos, consulte:

[Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
