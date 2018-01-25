---
title: Reimplantar a pilha do Azure | Microsoft Docs
description: Reimplante a pilha do Azure.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Reimplantar a pilha do Azure
Se você receber um erro durante a implantação da pilha do Azure, você pode executar novamente a instalação usando o seguinte comando do PowerShell: `.\InstallAzureStackpoc.ps1 -rerun`. Este comando vai reiniciar a instalação de pilha do Azure no ponto em que falhou anteriormente sem a necessidade de recomeçar desde o início. Se você receber o mesmo erro de instalação novamente, ele pode ser necessário executar uma reimplantação completa para endereçar o problema. 

Para reimplantar a pilha do Azure, você deve começar do zero conforme descrito abaixo.

## <a name="steps-to-redeploy-azure-stack"></a>Passos para reimplantar a pilha do Azure
1. No host do kit de desenvolvimento, abra um console do PowerShell com privilégios elevados > navegue até o script asdk installer.ps1 > executá-lo > clique **reinicializar**.
2. Selecione o sistema operacional base (não **Azure pilha**) e clique em **próximo**.
3. Após a reinicialização do host do kit de desenvolvimento, exclua o arquivo de CloudBuilder.vhdx que foi usado como parte da implantação anterior.
4. [Implantar o kit de desenvolvimento](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Próximas etapas
[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

