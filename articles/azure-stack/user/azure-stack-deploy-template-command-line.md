---
title: Implantar modelos com a linha de comando na pilha do Azure | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para implantar modelos na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316188"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implantar modelos no Azure Stack usando a linha de comando

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use a linha de comando para implantar modelos do Gerenciador de recursos do Azure para o Kit de desenvolvimento de pilha do Azure. Modelos do Gerenciador de recursos do Azure implantar e provisionar todos os recursos para seu aplicativo em uma única operação coordenado.

## <a name="before-you-begin"></a>Antes de começar
 - [Instalar e conectar](azure-stack-version-profiles-azurecli2.md) a pilha do Azure com a CLI do Azure
 - Baixe os arquivos *azuredeploy.json* e *azuredeploy.parameters.json* do [criar modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Implantar modelo
Navegue até a pasta onde esses arquivos foram baixados e execute o seguinte comando para implantar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Esse comando implanta o modelo para o grupo de recursos **cliRG** no local de padrão de POC de pilha do Azure.

## <a name="validate-template-deployment"></a>Validar a implantação de modelo
Para ver esse grupo de recursos e essa conta de armazenamento, use os seguintes comandos:

    azure group list

    azure storage account list




