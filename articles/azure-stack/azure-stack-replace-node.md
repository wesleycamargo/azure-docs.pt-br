---
title: "Substituir um nó de unidade de escala em um sistema de pilha do Azure integradas | Microsoft Docs"
description: "Saiba como substituir um nó de unidade de escala físico em um sistema de pilha do Azure integradas."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 4e5b1269e2bee31316cba99d69ea2a6d702faf05
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Substituir um nó de unidade de escala em um sistema de pilha do Azure integrado

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve o processo geral para substituir um computador físico (também conhecido como um *nó de unidade de escala*) em uma pilha do Azure sistema integrado. Substituição de nó de unidade de escala real etapas irão variar com base no seu fornecedor de hardware do fabricante (OEM). Consulte a documentação do fornecedor campo FRU (unidade renovável) para obter etapas detalhadas que são específicas para seu sistema.

O diagrama de fluxo a seguir mostra o processo geral de FRU para substituir um nó de unidade de escala inteiro.

![Gráfico de fluxo de processo do nó de substituição](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* Esta ação pode não ser necessária com base na condição do hardware física.

## <a name="review-alert-information"></a>Revise as informações de alerta

Se um nó de unidade de escala estiver inativo, você receberá todos os alertas críticos seguintes:

- Nó não está conectado ao controlador de rede
- Nó inacessível para o posicionamento de máquina virtual
- Nó de unidade de escala está offline

![Lista de alertas de unidade de escala para baixo](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Se você abrir o alerta "o nó de unidade de escala está offline", a descrição do alerta contém o nó de unidade de escala está inacessível. Você também pode receber alertas adicionais na solução de monitoramento específico do OEM que está em execução no host de ciclo de vida de hardware.

![Detalhes do alerta offline do nó](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Processo de substituição do nó de unidade de escala

As etapas a seguir são fornecidas como uma visão geral do processo de substituição do nó de unidade de escala. Consulte a documentação de FRU do fornecedor de hardware seu OEM para obter etapas detalhadas que são específicas para seu sistema. Não siga essas etapas sem fazer referência a documentação fornecida pelo OEM.

1. Use o [drenar](azure-stack-node-actions.md#scale-unit-node-actions) ação para colocar o nó de unidade de escala no modo de manutenção. Esta ação pode não ser necessária com base na condição do hardware física.

   > [!NOTE]
   > Em qualquer caso, somente um nó pode ser descarregado e desligá-la ao mesmo tempo sem quebrar SSD (espaços de armazenamento diretos).

2. Se o nó ainda está ligado, use o [desligue](azure-stack-node-actions.md#scale-unit-node-actions) ação. Esta ação pode não ser necessária com base na condição do hardware física.
 
   > [!NOTE]
   > No caso improvável de que a ação de desligamento não funciona, use a interface da web do baseboard management controlador BMC.

1. Substitua o computador físico. Normalmente, isso é feito pelo seu fornecedor de hardware de OEM.
2. Use o [reparo](azure-stack-node-actions.md#scale-unit-node-actions) ação para adicionar o novo computador físico para a unidade de escala.
3. Usar o ponto de extremidade com privilégios [verificar o status de reparo de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com novas unidades de dados, um trabalho de reparo de armazenamento completo pode levar várias horas, dependendo da carga do sistema e consumido espaço.
4. Após a ação de reparo, valide que todos os alertas ativos foi fechados automaticamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como substituir um disco físico trocados, consulte [substituir um disco](azure-stack-replace-disk.md). 
- Para obter informações sobre como substituir um componente de hardware não intercambiáveis, consulte [substituir um componente de hardware](azure-stack-replace-component.md). 
