---
title: Implantar modelos com o Visual Studio no Azure Stack | Microsoft Docs
description: Saiba como implantar modelos com o Visual Studio no Azure Stack.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 8fc32dc50d96d202dfc982cbdc52d8e479c3a3eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implantar modelos no Azure Stack usando o Visual Studio

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use o Visual Studio para implantar modelos do Azure Resource Manager o kit de desenvolvimento de pilha do Azure.

1. [Instalar e conectar](azure-stack-install-visual-studio.md) com o Azure Stack com o Visual Studio.
2. Abra o Visual Studio.
3. Clique em **Arquivo**, em **Novo** e, na caixa de diálogo **Novo Projeto**, clique em **Grupo de Recursos do Azure**.
4. Insira um **Nome** para o novo projeto e clique em **OK**.
5. Na caixa de diálogo **Selecionar Modelo do Azure**, altere a lista suspensa *Mostrar modelos deste local* para **Início Rápido do Azure Stack**
6. Clique em **101-criar--conta de armazenamento**e, em seguida, clique em **Okey**.  
7. É possível ver no novo projeto uma lista de modelos disponíveis expandindo o nó **Modelos** do painel **Gerenciador de Soluções**.
8. No painel **Gerenciador de Soluções**, clique com o botão direito do mouse no nome de seu projeto, clique em **Implantar** e em **Nova Implantação**.
9. Na caixa de diálogo **Implantar no Grupo de Recursos**, na lista suspensa **Assinatura**, selecione sua assinatura do Microsoft Azure Stack.
10. Na lista **Grupo de Recursos** , escolha um grupo de recursos existente ou crie um novo.
11. Na lista **Local do grupo de recursos**, escolha um local e clique em **Implantar**.
12. Na caixa de diálogo **Editar Parâmetros**, insira os valores dos parâmetros (que variam de acordo com o modelo) e clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
[Implantar modelos com a linha de comando](azure-stack-deploy-template-command-line.md)

[Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)

