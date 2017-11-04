---
title: Reimplantar a pilha do Azure | Microsoft Docs
description: Reimplante a pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Reimplantar a pilha do Azure
Para reimplantar a pilha do Azure, você deve começar do zero conforme descrito abaixo.

## <a name="steps-to-redeploy-azure-stack"></a>Passos para reimplantar a pilha do Azure
1. No host do kit de desenvolvimento, abra um console do PowerShell com privilégios elevados > navegue até o script asdk installer.ps1 > executá-lo > clique **reinicializar**.
2. Selecione o sistema operacional base (não **Azure pilha**) e clique em **próximo**.
3. Após a reinicialização do host do kit de desenvolvimento, exclua o arquivo de CloudBuilder.vhdx que foi usado como parte da implantação anterior.
4. [Implantar o kit de desenvolvimento](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Próximas etapas
[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

