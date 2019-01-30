---
title: Substituir um componente de hardware em um nó de unidade de escala do Azure Stack | Microsoft Docs
description: Saiba como substituir um componente de hardware em um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 7133c9de08a3a4f46bf8f2cc5f2de863f8efe57a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248469"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Substituir um componente de hardware em um nó de unidade de escala do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o processo geral para substituir os componentes de hardware que não trocados. Substituição real, as etapas variam com base em seu fornecedor de hardware do fabricante original do equipamento (OEM). Confira a documentação sobre a unidade substituível de campo (FRU) para obter etapas detalhadas que são específicas para seu sistema integrado do Azure Stack.

Componentes não intercambiáveis incluem o seguinte:

- CPU*
- Memória *
- Controlador de gerenciamento de placa-mãe/placa-base (BMC) / vídeo cartão
- Adaptador de barramento do host do controlador de disco (HBA) / backplane
- Adaptador de rede (NIC)
- Disco do sistema operacional *
- Unidades de dados (unidades que não dão suporte à troca a quente, por exemplo PCI-e placas de expansão) *

* Esses componentes podem dar suporte a hot swap, mas podem variar com base na implementação do fornecedor. Consulte a documentação de FRU do fornecedor OEM para obter etapas detalhadas.

O diagrama de fluxo a seguir mostra o processo geral de FRU para substituir um componente de hardware não intercambiáveis.

![Diagrama mostrando o fluxo de substituição de componente do fluxo](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Essa ação pode não ser necessária com base na condição do hardware física.

* * Se o fornecedor do hardware OEM executa a substituição de componentes e as atualizações que de firmware pode variar com base no seu contrato de suporte.

## <a name="review-alert-information"></a>Examine as informações de alerta

A integridade do Azure Stack e o sistema de monitoramento acompanham a integridade dos adaptadores de rede e unidades de dados controladas por espaços de armazenamento diretos. Não controla de outros componentes de hardware. Para todos os outros componentes de hardware, os alertas são gerados no hardware específicos do fornecedor que é executado no host de ciclo de vida do hardware solução de monitoramento.  

## <a name="component-replacement-process"></a>Processo de substituição de componente

As etapas a seguir fornecem uma visão geral do processo de substituição de componente. Não siga estas etapas sem fazer referência a documentação do FRU fornecidos pelo OEM.

1. Use a ação de desligamento para desligar normalmente o nó de unidade de escala. Essa ação pode não ser necessária com base na condição do hardware física.

2. Em um caso improvável da ação de desligamento falhar, use o [drenar](azure-stack-node-actions.md#drain) ação para colocar o nó de unidade de escala no modo de manutenção. Essa ação pode não ser necessária com base na condição do hardware física.

   > [!NOTE]  
   > Em qualquer caso, apenas um nó pode ser desativado e desligado ao mesmo tempo sem quebrar o S2D (espaços de armazenamento diretos).

3. Após o nó de unidade de escala no modo de manutenção, use o [desligue](azure-stack-node-actions.md#scale-unit-node-actions) ação. Essa ação pode não ser necessária com base na condição do hardware física.

   > [!NOTE]  
   > No caso improvável de que a ação de desligar não funciona, use a interface da web do baseboard management controller (BMC).

4. Substitua o componente de hardware danificado. Se o fornecedor do hardware OEM executa a substituição de componente pode variar com base no seu contrato de suporte.  
5. Atualize o firmware. Siga seu processo de atualização de firmware específico do fornecedor usando o host de ciclo de vida do hardware para garantir que o componente de hardware substituído tem o firmware aprovado nível aplicado. Se o fornecedor do hardware OEM realiza esta etapa pode variar de acordo com seu contrato de suporte.  
6. Use o [reparo](azure-stack-node-actions.md#scale-unit-node-actions) ação para que o nó de unidade de escala de volte para a unidade de escala.
7. Use o ponto de extremidade com privilégios [verificar o status de reparo de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com novas unidades de dados, um trabalho de reparo de armazenamento completo pode levar várias horas, dependendo da carga do sistema e espaço consumido.
8. Após a ação de reparo, valide que todos os alertas ativos foi fechados automaticamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como substituir um disco físico intercambiáveis, consulte [substituir um disco](azure-stack-replace-disk.md).
- Para obter informações sobre como substituir um nó físico, consulte [substituir um nó de unidade de escala](azure-stack-replace-node.md).
