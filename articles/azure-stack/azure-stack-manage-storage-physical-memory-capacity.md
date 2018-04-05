---
title: Gerenciar a capacidade de memória física para a pilha do Azure | Microsoft Docs
description: Monitorar e gerenciar o espaço de armazenamento disponível para a pilha do Azure.
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
ms.date: 04/03/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: 34b03cf1fda29706936968ab93cb35a6a2510c65
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Gerenciar a capacidade de memória física para a pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Para aumentar a capacidade de memória total disponível para a pilha do Azure, você pode adicionar mais memória. Na pilha do Azure seu servidor físico também é conhecido como um *nó de unidade de escala*. Todos os nós de unidade de escala que são membros de uma unidade de escala única devem ter a mesma quantidade de memória.

> [!note]  
> Antes de continuar, consulte a documentação do fabricante de hardware para ver se uma memória atualiza seu fabricante dá suporte a uma atualização de memória físico. Seu contrato de suporte do fornecedor de hardware OEM pode exigir que o fornecedor de executar o posicionamento de rack do servidor físico e a atualização de firmware do dispositivo.

O diagrama de fluxo a seguir mostra o processo geral para adicionar memória para cada nó de unidade de escala.

![Adicione memória em cada nó de unidade de escala](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Adicionar memória a um nó existente
As etapas a seguir fornecem uma visão geral do processo adicionar memória. 

> [!Warning]  
Não siga essas etapas sem fazer referência a documentação fornecida pelo OEM.

> [!Warning]  
A unidade de escala inteiro deve ser desligada como não há suporte para uma atualização sem interrupção de memória.

1. Parar a pilha do Azure usando as etapas documentadas no [iniciar e parar Azure pilha](azure-stack-start-and-stop.md) artigo.
2. Atualize a memória em cada computador físico usando a documentação do fabricante do hardware.
3. Iniciar a pilha do Azure usando as etapas a [iniciar e parar Azure pilha](azure-stack-start-and-stop.md) artigo.

## <a name="next-steps"></a>Próximas etapas

 - Para saber como gerenciar contas de armazenamento na pilha do Azure para localizar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócios, consulte [gerenciar contas de armazenamento no Azure pilha](azure-stack-manage-storage-accounts.md).
 - Para saber o operador de nuvem de pilha do Azure monitora e gerencia a capacidade de armazenamento de sua implantação de pilha do Azure, consulte [gerenciar a capacidade de armazenamento do Azure pilha](azure-stack-manage-storage-shares.md). 