---
title: "Problemas de reinicialização ou redimensionamento da VM | Microsoft Docs"
description: "Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual Linux no Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 51f1610c-0290-464a-97d4-c2e485c7ae7f
ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.workload: required
ms.date: 09/09/2016
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9f85988268d4c832c46da1790c83d4a78141f489


---
# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual Linux no Azure
Ao tentar iniciar uma VM (Máquina Virtual) do Azure parada ou redimensionar uma VM do Azure existente, o erro comum encontrado é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou quando não dá suporte ao tamanho de VM solicitado.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Coletar logs de auditoria
Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo:

[Solução de problemas de implantações de grupos de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: erro ao iniciar uma VM parada
Você tenta iniciar uma VM parada, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
Deve-se tentar fazer a solicitação de início da VM parada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### <a name="resolution"></a>Resolução
* Parar todas as VMs no conjunto de disponibilidade e, em seguida, reiniciar cada VM.
  
  1. Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  2. Depois que todas as VMs pararem, selecione cada uma das VMs paradas e clique em Iniciar.
* Repita a solicitação de reinicialização mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: erro ao redimensionar uma VM existente
Você tenta redimensionar uma VM existente, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
Deve-se tentar fazer a solicitação de redimensionamento da VM no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não dá suporte ao tamanho de VM solicitado.

### <a name="resolution"></a>Resolução
* Repita a solicitação com um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  
  1. Pare todas as VMs no conjunto de disponibilidade.
     
     * Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  2. Depois de parar todas as VMs, redimensione a VM desejada para um tamanho maior.
  3. Selecione a VM redimensionada, clique em **Iniciar**e inicie cada uma das VMs paradas.

## <a name="next-steps"></a>Próximas etapas
Se você encontrar problemas ao criar uma nova VM do Linux no Azure, veja [Solucionar problemas de implantação com a criação de uma nova máquina virtual do Linux no Azure](virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Nov16_HO3-->


