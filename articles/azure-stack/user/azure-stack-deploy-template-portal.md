---
title: Implantar modelos usando o portal na pilha do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure Stack para implantar modelos.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604226"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implantar modelos usando o portal do Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o portal para implantar modelos do Azure Resource Manager pilha do Azure.

## <a name="to-deploy-a-template"></a>Para implantar um modelo

1. Entre no portal, selecione **novo**e, em seguida, selecione **personalizado**.
2. Selecione **implantação de modelo**.
3. Selecione **Editar modelo**e, em seguida, cole o código de modelo JSON na janela de código. Clique em **Salvar**.
4. Selecione **Editar parâmetros**, forneça valores para os parâmetros que são mostrados e, em seguida, selecione **Okey**.
5. Selecione **assinatura**. Escolha a assinatura que você deseja usar e, em seguida, selecione **Okey**.
6. Selecione **grupo de recursos**. Escolha um grupo de recursos existente ou crie um novo e, em seguida, selecione **Okey**.
7. Clique em **Criar**. Um novo bloco no painel acompanha o progresso da implantação de modelo.

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
