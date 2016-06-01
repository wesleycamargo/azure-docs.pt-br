<properties
   pageTitle="Problemas de reinicialização ou redimensionamento de VM | Microsoft Azure"
   description="Solucionar problemas de implantação clássica ao reinicializar ou redimensionar uma Máquina Virtual Linux existente no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="required"
   ms.date="05/12/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# Solucionar problemas de implantação clássica ao reinicializar ou redimensionar uma Máquina Virtual Linux existente no Azure

> [AZURE.SELECTOR]
- [Clássico](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Gerenciador de Recursos](../articles/virtual-machines/virtual-machines-linux-restart-resize-error-troubleshooting.md)

Ao tentar iniciar uma VM (Máquina Virtual) do Azure parada ou redimensionar uma VM do Azure existente, o erro comum encontrado é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou quando não dá suporte ao tamanho de VM solicitado.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Coletar logs de auditoria

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado ao problema.

No portal do Azure, clique em **Procurar** > **Máquinas virtuais** > _sua máquina virtual Linux_ > **Configurações** > **Logs de Auditoria**.

## Problema: erro ao iniciar uma VM parada

Você tenta iniciar uma VM parada, mas ocorre uma falha de alocação.

### Causa

Deve-se tentar fazer a solicitação de início da VM parada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### Resolução

* Crie um novo serviço de nuvem e o associe a uma região ou a uma rede virtual baseada em região, mas não a um grupo de afinidades.

* Exclua a VM parada.

* Recrie a VM no novo serviço de nuvem usando os discos.

* Inicie a VM recriada.

Se você receber um erro ao tentar criar um novo serviço de nuvem, tente novamente mais tarde ou altere a região do serviço de nuvem.

> [AZURE.IMPORTANT] O novo serviço de nuvem terá um novo nome e VIP e, portanto, será necessário alterar essas informações em todas as dependências que usam essas informações para o serviço de nuvem existente.

## Problema: erro ao reiniciar uma VM existente

Você tenta redimensionar uma VM existente, mas ocorre uma falha de alocação.

### Causa

Deve-se tentar fazer a solicitação de redimensionamento da VM no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não dá suporte ao tamanho de VM solicitado.

### Resolução

Reduza o tamanho de VM solicitado e tente fazer novamente a solicitação de redimensionamento.

* Clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > _sua máquina virtual_ > **Configurações** > **Tamanho**. Para obter as etapas detalhadas, consulte [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Se não for possível reduzir o tamanho da VM, siga estas etapas:

  * Crie um novo serviço de nuvem, garantindo que ele não esteja vinculado a um grupo de afinidades nem associado a uma rede virtual vinculada a um grupo de afinidades.

  * Crie uma nova VM maior nele.

É possível consolidar todas as VMs no mesmo serviço de nuvem. Se o serviço de nuvem existente estiver associado a uma rede virtual baseada em região, será possível conectar o novo serviço de nuvem à rede virtual existente.

Se o serviço de nuvem existente não estiver associado a uma rede virtual baseada em região, será necessário excluir as VMs no serviço de nuvem existente e recriá-las no novo serviço de nuvem por meio de seus discos. No entanto, é importante lembrar-se de que o novo serviço de nuvem terá um novo nome e VIP e, portanto, será necessário atualizá-los em todas as dependências que atualmente usam essas informações para o serviço de nuvem existente.

<!---HONumber=AcomDC_0518_2016-->