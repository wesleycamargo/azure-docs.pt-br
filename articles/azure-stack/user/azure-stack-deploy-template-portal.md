---
title: Implantar modelos com o portal na pilha do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure Stack para implantar modelos.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implantar modelos usando o portal do Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use o portal para implantar modelos do Azure Resource Manager o kit de desenvolvimento de pilha do Azure.

Modelos do Gerenciador de recursos implantar e provisionar todos os recursos para seu aplicativo em uma única operação coordenado.

1. Faça logon no portal, clique em **novo**, clique em **personalizado**e, em seguida, clique em **implantação de modelo**.
2. Clique em **Editar modelo**, cole o código de modelo JSON a folha e, em seguida, clique em **salvar**.
3. Clique em **Editar parâmetros**, digite valores para os parâmetros listados e, em seguida, clique em **Okey**.
4. Clique em **assinatura**, escolha a assinatura que você deseja usar e, em seguida, clique em **Okey**.
5. Clique em **grupo de recursos**, escolha um grupo de recursos existente ou crie um novo e, em seguida, clique em **Okey**.
6. Clique em **Criar**. Um novo bloco no painel acompanha o progresso da implantação de modelo.

## <a name="next-steps"></a>Próximas etapas
[Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)

