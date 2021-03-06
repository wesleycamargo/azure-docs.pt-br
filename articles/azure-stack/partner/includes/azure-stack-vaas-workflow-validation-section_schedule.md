---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: e9d59fadb9cbfeb7463f799bdab2e6da49208fe9
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57343476"
---
Nos fluxos de trabalho validação **agendamento** um teste usa os parâmetros comuns de nível de fluxo de trabalho que você especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](../azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se tornar inválido, deve resupply-las conforme as instruções [modifique os parâmetros de fluxo de trabalho](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Um teste de validação no agendamento sobre uma instância existente, você criará uma nova instância no lugar da instância antiga no portal. Os logs para a instância antiga serão retidos, mas não são acessíveis a partir do portal.  
Depois que um teste for concluído com êxito, o **agendamento** ação for desabilitada.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Selecione **agendamento** no menu de contexto para abrir um prompt para agendar a instância de teste.

1. Revise os parâmetros de teste e, em seguida, selecione **enviar** para agendar o teste para execução.