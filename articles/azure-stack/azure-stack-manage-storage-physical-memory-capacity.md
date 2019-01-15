---
title: Gerenciar a capacidade de memória física para o Azure Stack | Microsoft Docs
description: Monitorar e gerenciar o espaço de armazenamento disponível para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 928e4c7b6f7711dcad8190b6c368bd7f1df2b66c
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305886"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Gerenciar a capacidade de memória física para o Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Para aumentar a capacidade de memória total disponível para o Azure Stack, você pode adicionar mais memória. No Azure Stack seu servidor físico também é conhecido como um *nó de unidade de escala*. Todos os nós de unidade de escala que são membros de uma única unidade de escala devem ter a mesma quantidade de memória.

> [!note]  
> Antes de continuar, consulte a documentação do fabricante do hardware para ver se um fabricante do dá suporte a uma atualização de memória física. Seu contrato de suporte do fornecedor de hardware OEM pode exigir que o fornecedor de executar o posicionamento de rack de servidor físico e a atualização de firmware do dispositivo.

O diagrama de fluxo a seguir mostra o processo geral para adicionar memória a cada nó de unidade de escala.

![Adicione memória em cada nó de unidade de escala](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Adicionar memória a um nó existente
As etapas a seguir fornecem uma visão geral do processo de memória a adicionar. 

> [!Warning]  
Não siga estas etapas sem fazer referência a documentação fornecida pelo OEM.

> [!Warning]  
A unidade de escala inteira deve ser desligada como não há suporte para uma atualização sem interrupção de memória.

1. Parar o Azure Stack usando as etapas documentadas na [iniciar e parar o Azure Stack](azure-stack-start-and-stop.md) artigo.
2. Atualize a memória em cada computador físico usando a documentação do fabricante do hardware.
3. Inicie o Azure Stack usando as etapas a [iniciar e parar o Azure Stack](azure-stack-start-and-stop.md) artigo.

## <a name="next-steps"></a>Próximas etapas

 - Para saber como gerenciar contas de armazenamento no Azure Stack para encontrar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócios, consulte [gerenciar contas de armazenamento no Azure Stack](azure-stack-manage-storage-accounts.md).
 - Para saber o operador de nuvem do Azure Stack monitora e gerencia a capacidade de armazenamento de sua implantação do Azure Stack, consulte [gerenciar a capacidade de armazenamento para o Azure Stack](azure-stack-manage-storage-shares.md). 
