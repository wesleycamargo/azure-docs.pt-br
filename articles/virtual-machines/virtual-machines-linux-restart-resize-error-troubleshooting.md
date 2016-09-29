<properties
   pageTitle="Problemas de reinicialização ou redimensionamento de VM | Microsoft Azure"
   description="Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual Linux no Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual Linux no Azure

Ao tentar iniciar uma VM (Máquina Virtual) do Azure parada ou redimensionar uma VM do Azure existente, o erro comum encontrado é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou quando não dá suporte ao tamanho de VM solicitado.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Coletar logs de auditoria

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo:

[Solução de problemas de implantações de grupos de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)

## Problema: erro ao iniciar uma VM parada

Você tenta iniciar uma VM parada, mas ocorre uma falha de alocação.

### Causa

Deve-se tentar fazer a solicitação de início da VM parada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### Resolução

*	Parar todas as VMs no conjunto de disponibilidade e, em seguida, reiniciar cada VM.

  1. Clique em **Grupos de recursos** > _seu grupo de recursos_ > **Recursos** > _seu conjunto de disponibilidade_ > **Máquinas Virtuais** > _sua máquina virtual_ > **Parar**.

  2. Depois que todas as VMs pararem, selecione cada uma das VMs paradas e clique em Iniciar.

*	Repita a solicitação de reinicialização mais tarde.

## Problema: erro ao redimensionar uma VM existente

Você tenta redimensionar uma VM existente, mas ocorre uma falha de alocação.

### Causa

Deve-se tentar fazer a solicitação de redimensionamento da VM no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não dá suporte ao tamanho de VM solicitado.

### Resolução

* Repita a solicitação com um tamanho de VM menor.

* Se o tamanho da VM solicitada não puder ser alterado:

  1. Pare todas as VMs no conjunto de disponibilidade.

    * Clique em **Grupos de recursos** > _seu grupo de recursos_ > **Recursos** > _seu conjunto de disponibilidade_ > **Máquinas Virtuais** > _sua máquina virtual_ > **Parar**.

  2. Depois de parar todas as VMs, redimensione a VM desejada para um tamanho maior.
  3. Selecione a VM redimensionada, clique em **Iniciar** e inicie cada uma das VMs paradas.

## Próximas etapas

Se você encontrar problemas ao criar uma nova VM do Linux no Azure, veja [Solucionar problemas de implantação com a criação de uma nova máquina virtual do Linux no Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).

<!---HONumber=AcomDC_0914_2016-->