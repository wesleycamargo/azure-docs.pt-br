---
title: Implantar modelos com o Visual Studio no Azure Stack | Microsoft Docs
description: Saiba como implantar modelos com o Visual Studio no Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 6cd722fedc0483e37ce6ee491d74a7c985111353
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605116"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implantar modelos no Azure Stack usando o Visual Studio

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o Visual Studio para implantar modelos do Azure Resource Manager pilha do Azure.

## <a name="to-deploy-a-template"></a>Para implantar um modelo

1. [Instalar e conectar](azure-stack-install-visual-studio.md) com o Azure Stack com o Visual Studio.
2. Abra o Visual Studio.
3. Selecione **arquivo**e, em seguida, selecione **novo**. Em **novo projeto**, selecione **o grupo de recursos do Azure**.
4. Insira um **nome** para o novo projeto e selecione **Okey**.
5. Em **Selecionar modelo do Azure**, escolha **início rápido do Azure pilha** na lista suspensa.
6. Selecione **101-criar--conta de armazenamento**e, em seguida, selecione **Okey**.
7. No seu novo projeto, expanda o **modelos** nó **Solution Explorer** para ver os modelos disponíveis.
8. Em **Solution Explorer**, escolha o nome do seu projeto e, em seguida, selecione **implantar**. Selecione **nova implantação**.
9. Em **implantar para grupo de recursos**, use o **assinatura** lista suspensa para selecionar a assinatura de pilha do Microsoft Azure.
10. Do **grupo de recursos** lista, escolha um grupo de recursos existente ou crie um novo.
11. Do **local do grupo de recursos** lista, escolha um local e, em seguida, selecione **implantar**.
12. Em **Editar parâmetros**, forneça valores para os parâmetros (que variam de acordo com o modelo) e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com a linha de comando](azure-stack-deploy-template-command-line.md)
* [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
