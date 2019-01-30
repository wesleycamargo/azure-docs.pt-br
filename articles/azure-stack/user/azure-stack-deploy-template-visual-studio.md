---
title: Implantar modelos com o Visual Studio no Azure Stack | Microsoft Docs
description: Saiba como implantar modelos com o Visual Studio no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: f4fda8bbbb1bf88934f641644f89c319317ec58f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55237502"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implantar modelos no Azure Stack usando o Visual Studio

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o Visual Studio para implantar modelos do Azure Resource Manager para o Azure Stack.

## <a name="to-deploy-a-template"></a>Implantar um modelo

1. [Instalar e conectar](azure-stack-install-visual-studio.md) com o Azure Stack com o Visual Studio.
2. Abra o Visual Studio.
3. Selecione **arquivo**e, em seguida, selecione **New**. Na **novo projeto**, selecione **grupo de recursos do Azure**.
4. Insira um **nome** para o novo projeto e, em seguida, selecione **Okey**.
5. Na **Selecionar modelo do Azure**, escolha **guia de início rápido do Azure Stack** na lista suspensa.
6. Selecione **101-create-storage-account**e, em seguida, selecione **Okey**.
7. No seu novo projeto, expanda o **modelos** nó no **Gerenciador de soluções** para ver os modelos disponíveis.
8. Na **Gerenciador de soluções**, escolha o nome do seu projeto e, em seguida, selecione **implantar**. Selecione **nova implantação**.
9. Na **implantar no grupo de recursos**, use o **assinatura** lista suspensa para selecionar sua assinatura do Microsoft Azure Stack.
10. Dos **grupo de recursos** lista, escolha um grupo de recursos existente ou crie um novo.
11. Dos **local do grupo de recursos** lista, escolha um local e, em seguida, selecione **implantar**.
12. Na **Editar parâmetros**, forneça valores para os parâmetros (que variam de acordo com o modelo) e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com a linha de comando](azure-stack-deploy-template-command-line.md)
* [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
