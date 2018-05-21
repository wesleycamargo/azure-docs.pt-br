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
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 278f15271d3a5443102f5e387d3db1adb53fe7db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implantar modelos usando o portal do Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o portal para implantar modelos do Azure Resource Manager pilha do Azure.

Para implantar um modelo:

1. Entre no portal, selecione **novo**e, em seguida, selecione **personalizado**.
2. Selecione **implantação de modelo**.
3. Selecione **Editar modelo**e, em seguida, cole o código de modelo JSON na janela de código. Clique em **Salvar**.
4. Selecione **Editar parâmetros**, forneça valores para os parâmetros que são mostrados e, em seguida, selecione **Okey**.
5. Selecione **assinatura**. Escolha a assinatura que você deseja usar e, em seguida, selecione **Okey**.
6. Selecione **grupo de recursos**. Escolha um grupo de recursos existente ou crie um novo e, em seguida, selecione **Okey**.
7. Clique em **Criar**. Um novo bloco no painel acompanha o progresso da implantação de modelo.

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
