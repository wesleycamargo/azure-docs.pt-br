---
title: "Substituir um componente de hardware em um nó de unidade de escala de pilha do Azure | Microsoft Docs"
description: Saiba como substituir um componente de hardware em um sistema de pilha do Azure integradas.
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 430274025bc6cd657a733fb80feb6cd7b39a36f3
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Substituir um componente de hardware em um nó de unidade de escala de pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve o processo geral para substituir os componentes de hardware que não trocados. Substituição real etapas irão variar com base no seu fornecedor de hardware do fabricante (OEM). Consulte a documentação do fornecedor campo FRU (unidade renovável) para obter etapas detalhadas que são específicas para seu sistema de pilha do Azure integradas.

Componentes não intercambiáveis incluem o seguinte:

- CPU *
- Memória *
- Placa-mãe/controlador BMC (BMC) / vídeo cartão
- Adaptador de barramento de host do controlador de disco (HBA) / backplane
- Adaptador de rede (NIC)
- Sistema operacional do disco *
- Unidades de dados (unidades que não dão suporte a troca a quente, por exemplo PCI-e placas de expansão) *

* Esses componentes podem dar suporte a troca a quente, mas podem variar com base na implementação do fornecedor. Consulte a documentação de FRU do fornecedor do seu OEM para obter etapas detalhadas.

O diagrama de fluxo a seguir mostra o processo geral de FRU para substituir um componente de hardware não removível.

![Fluxograma mostrando o fluxo de substituição de componente](media/azure-stack-replace-component/ReplaceComponentFlow.PNG)

* Esta ação pode não ser necessária com base na condição do hardware física.

* * Se o fornecedor do hardware OEM executa a substituição de componente e atualiza o firmware podem variar com base no oferece suporte ao contrato.

## <a name="review-alert-information"></a>Revise as informações de alerta

A integridade de pilha do Azure e o sistema de monitoramento monitora a integridade dos adaptadores de rede e unidades de dados que são controladas por espaços de armazenamento diretos. Ele não monitora outros componentes de hardware. Para todos os outros componentes de hardware, os alertas são gerados no hardware específicos de fornecedor solução que é executado no host de ciclo de vida do hardware de monitoramento.

## <a name="component-replacement-process"></a>Processo de substituição de componente

As etapas a seguir são fornecidas como uma visão geral do processo de substituição de componente. Não siga essas etapas sem fazer referência a documentação do FRU fornecidos pelo OEM.

1. Use o [drenar](azure-stack-node-actions.md#scale-unit-node-actions) ação para colocar o nó de unidade de escala no modo de manutenção. Esta ação pode não ser necessária com base na condição do hardware física.
2. Após o nó de unidade de escala no modo de manutenção, use o [desligue](azure-stack-node-actions.md#scale-unit-node-actions) ação. Esta ação pode não ser necessária com base na condição do hardware física.
 
   > [!NOTE]
   > No caso improvável de que a ação de desligamento não funciona, use a interface da web do baseboard management controlador BMC.

3. Substitua o componente de hardware danificado. Se o fornecedor do hardware OEM executa a substituição de componente pode variar com base no seu contrato de suporte.  
4. Atualize o firmware. Siga o processo de atualização de firmware específicas de fornecedor usando o host de ciclo de vida de hardware para certificar-se de que o componente de hardware substituído tem o firmware de aprovados nível aplicado. Se o fornecedor do hardware OEM executa essa etapa pode variar de acordo com seu contrato de suporte.  
5. Use o [reparo](azure-stack-node-actions.md#scale-unit-node-actions) ação para que o nó de unidade de escala volte para a unidade de escala.
6. Usar o ponto de extremidade com privilégios [verificar o status de reparo de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com novas unidades de dados, um trabalho de reparo de armazenamento completo pode levar várias horas, dependendo da carga do sistema e consumido espaço.
7. Após a ação de reparo, valide que todos os alertas ativos foi fechados automaticamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como substituir um disco físico trocados, consulte [substituir um disco](azure-stack-replace-disk.md).
- Para obter informações sobre a substituição de um nó físico, consulte [substituir um nó de unidade de escala](azure-stack-replace-node.md). 