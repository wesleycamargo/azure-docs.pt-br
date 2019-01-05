---
title: Implantar modelos usando o portal do Azure Stack | Microsoft Docs
description: Saiba como usar o portal do Azure Stack para implantar modelos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 63f0c1846a6e32dc5a5fcc9b1162d343890387a1
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053510"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implantar modelos usando o portal do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o portal para implantar modelos do Azure Resource Manager para o Azure Stack.

## <a name="to-deploy-a-template"></a>Implantar um modelo

1. Entre no portal, selecione **+ criar um recurso**e, em seguida, selecione **personalizado**.
2. Selecione **Implantação de modelo**.
3. Selecione **Editar modelo**e, em seguida, cole o código de modelo JSON na janela de código. Clique em **Salvar**.
4. Selecione **Editar parâmetros**, forneça valores para os parâmetros que são mostrados e, em seguida, selecione **Okey**.
5. Selecione **assinatura**. Escolha a assinatura que você deseja usar e, em seguida, selecione **Okey**.
6. Selecione **grupo de recursos**. Escolha um grupo de recursos existente ou crie um novo e, em seguida, selecione **Okey**.
7. Selecione **Criar**. Um novo bloco no painel acompanha o progresso da implantação de modelo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como implantar modelos, consulte o artigo a seguir:

- [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
