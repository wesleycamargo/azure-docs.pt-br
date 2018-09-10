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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139724"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implantar modelos no Azure Stack usando a linha de comando

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Use a linha de comando para implantar modelos do Azure Resource Manager para o Kit de desenvolvimento do Azure Stack. Modelos do Gerenciador de recursos do Azure implantam e provisionam todos os recursos para seu aplicativo em uma única operação coordenada.

## <a name="before-you-begin"></a>Antes de começar
 - [Instalar e conectar](azure-stack-version-profiles-azurecli2.md) para o Azure Stack com CLI do Azure
 - Baixar os arquivos *azuredeploy. JSON* e *azuredeploy.parameters.json* do [criar modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Implantar modelo
Navegue até a pasta onde esses arquivos foram baixados e execute o seguinte comando para implantar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Esse comando implanta o modelo para o grupo de recursos **cliRG** no local de padrão do Azure Stack POC.

## <a name="validate-template-deployment"></a>Validar a implantação de modelo
Para ver esse grupo de recursos e essa conta de armazenamento, use os seguintes comandos:

    azure group list

    azure storage account list




