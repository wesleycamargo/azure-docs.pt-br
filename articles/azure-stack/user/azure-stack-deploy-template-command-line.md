---
title: Implantar modelos com a linha de comando na pilha do Azure | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para implantar modelos na pilha do Azure.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implantar modelos no Azure Stack usando a linha de comando

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use a linha de comando para implantar modelos do Gerenciador de recursos do Azure para o Kit de desenvolvimento de pilha do Azure. Modelos do Gerenciador de recursos do Azure implantar e provisionar todos os recursos para seu aplicativo em uma única operação coordenado.

## <a name="before-you-begin"></a>Antes de começar
 - [Instalar e conectar](azure-stack-connect-cli.md) a pilha do Azure com a CLI do Azure
 - Baixe os arquivos *azuredeploy.json* e *azuredeploy.parameters.json* do [criar modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Implantar modelo
Navegue até a pasta onde esses arquivos foram baixados e execute o seguinte comando para implantar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Esse comando implanta o modelo para o grupo de recursos **cliRG** no local de padrão de POC de pilha do Azure.

## <a name="validate-template-deployment"></a>Validar a implantação de modelo
Para ver esse grupo de recursos e essa conta de armazenamento, use os seguintes comandos:

    azure group list

    azure storage account list




