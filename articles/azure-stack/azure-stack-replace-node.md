---
title: Substituir um nó de unidade de escala em um sistema integrado do Azure Stack | Microsoft Docs
description: Saiba como substituir um nó de unidade de escala físico em um sistema integrado do Azure Stack.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 1024661fabfd97ce5f058d071de514fa7d02e527
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782302"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Substituir um nó de unidade de escala em um sistema integrado do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o processo geral para substituir um computador físico (também conhecido como um nó de unidade de escala) em um sistema integrado do Azure Stack. Substituição de nó de unidade de escala real etapas irão variar com base em seu fornecedor de hardware do fabricante original do equipamento (OEM). Consulte a documentação do fornecedor campo FRU (unidade) para obter etapas detalhadas que são específicas para seu sistema.

O diagrama de fluxo a seguir mostra o processo geral de FRU para substituir um nó de unidade de escala inteira.

![Fluxograma de processo de substituição de nó](media/azure-stack-replace-node/replacenodeflow.png)

* Esta ação pode não ser necessária com base na condição do hardware física.

> [!Note]  
> Se a operação de desligamento falhar, é recomendável usar a operação de drenagem seguida da operação de parada. Para obter mais detalhes, consulte operações de nó disponível  

## <a name="review-alert-information"></a>Examine as informações de alerta

Se um nó de unidade de escala estiver inativo, você receberá os seguintes alertas críticos:

- Nó não está conectado ao controlador de rede
- Nó inacessível para o posicionamento de máquina virtual
- Nó de unidade de escala está offline

![Lista de alertas de unidade de escala para baixo](media/azure-stack-replace-node/nodedownalerts.png)

Se você abrir o **nó de unidade de escala está off-line** contém a descrição do alerta alerta, o nó de unidade de escala que está inacessível. Você também pode receber alertas adicionais na solução de monitoramento específico do OEM que está em execução no host de ciclo de vida do hardware.

![Detalhes de alerta de nó offline](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Processo de substituição do nó de unidade de escala

As etapas a seguir são fornecidas como uma visão geral do que o processo de substituição de nó de unidade de escala. Consulte a documentação de FRU do fornecedor de hardware seu OEM para obter etapas detalhadas que são específicas para seu sistema. Não siga estas etapas sem fazer referência a documentação fornecida pelo OEM.

1. Use o **desligamento** ação para desligar normalmente o nó de unidade de escala. Essa ação pode não ser necessária com base na condição do hardware física. 

2. Na improvável que caso, a falha na ação de desligamento, use o [drenar](azure-stack-node-actions.md#drain) ação para colocar o nó de unidade de escala no modo de manutenção. Essa ação pode não ser necessária com base na condição do hardware física.

   > [!NOTE]  
   > Em qualquer caso, apenas um nó pode ser desativado e desligado ao mesmo tempo sem quebrar o S2D (espaços de armazenamento diretos).

3. Após o nó de unidade de escala no modo de manutenção, use o [parar](azure-stack-node-actions.md#stop) ação. Essa ação pode não ser necessária com base na condição do hardware física.

   > [!NOTE]  
   > No caso improvável de que a ação de desligar não funciona, use a interface da web do baseboard management controller (BMC).

4. Substitua o computador físico. Normalmente, isso é feito pelo seu fornecedor de hardware de OEM.
5. Use o [reparo](azure-stack-node-actions.md#repair) ação para adicionar o novo computador físico para a unidade de escala.
6. Use o ponto de extremidade com privilégios [verificar o status de reparo de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com novas unidades de dados, um trabalho de reparo de armazenamento completo pode levar várias horas, dependendo da carga do sistema e espaço consumido.
7. Após a ação de reparo, valide que todos os alertas ativos foi fechados automaticamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como substituir um disco físico, enquanto o sistema está ligado, consulte [substituir um disco](azure-stack-replace-disk.md). 
- Para obter informações sobre como substituir um componente de hardware que requer que o sistema seja desligado, consulte [substituir um componente de hardware](azure-stack-replace-component.md).
