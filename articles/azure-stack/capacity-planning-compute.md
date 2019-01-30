---
title: Capacidade de computação de planejamento para o Azure Stack | Microsoft Docs
description: Saiba mais sobre computação planejamento de capacidade para implantações do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 09/18/2018
ms.custom: mvc
ms.openlocfilehash: e756b48003ebfaff98271d93a3d8f0231571b5f9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242426"
---
# <a name="azure-stack-compute-capacity-planning"></a>Planejamento de capacidade de computação do Azure Stack
O [tamanhos VM com suporte no Azure Stack](./user/azure-stack-vm-sizes.md) são um subconjunto daquelas com suporte no Azure. O Azure impõe limites de recursos ao longo de muitos vetores para evitar o excesso de consumo de recursos (servidor de local e o nível de serviço). Sem impor alguns limites no consumo de locatários, as experiências de locatário serão afetada quando outros locatários overconsume recursos. Para a saída de rede da VM, há limites de largura de banda em vigor no Azure Stack que correspondem à limitações do Azure. Para recursos de armazenamento, limites de IOPs de armazenamento foram implementados no Azure Stack para evitar básico excesso de consumo de recursos por locatários para acesso de armazenamento.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Posicionamento de VM e excesso de provisionamento de núcleo virtual para física
No Azure Stack, não há nenhuma maneira de usar para o posicionamento de VM para um locatário especificar um servidor específico. A única consideração ao colocar as VMs é se há memória suficiente no host para esse tipo VM. O Azure Stack não sobrecarregue a memória; No entanto, uma superconfirmação do número de núcleos é permitida. Uma vez que os algoritmos de posicionamento não observar existente virtual para a taxa de excesso de provisionamento de núcleo físico como um fator, cada host pode ter uma taxa diferente. 

No Azure, para obter alta disponibilidade de um sistema de produção de várias VMs, as VMs são colocadas em um conjunto sejam distribuídas em vários domínios de falha. No Azure Stack, um domínio de falha em um conjunto de disponibilidade é definido como um único nó na unidade de escala.

Enquanto a infraestrutura do Azure Stack é resistente a falhas, a tecnologia subjacente (clustering de failover) ainda incorrerá em algum tempo de inatividade para as VMs em um servidor físico afetado em caso de falha de hardware. Atualmente, Azure Stack oferece suporte a uma conjunto de disponibilidade com um máximo de três domínios de falha para ser consistente com o Azure. As VMs colocadas em um conjunto de disponibilidade será fisicamente isoladas uns dos outros, distribuindo-los de maneira mais uniforme possível em vários domínios de falha (nós do Azure Stack). Se houver uma falha de hardware, VMs a partir do domínio de falha com falha serão ser reiniciadas em outros nós, mas, se possível, mantidas em domínios de falha separados de outras VMs no mesmo conjunto de disponibilidade. Quando o hardware fica online novamente, as VMs serão redistribuídas para manter a alta disponibilidade.

Outro conceito que é usado pelo Azure para fornecer alta disponibilidade é na forma de domínios de atualização em conjuntos de disponibilidade. Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. No Azure Stack, VMs estão ao vivo migrados entre os hosts online no cluster antes de seu host subjacente é atualizado. Como não há nenhum tempo de inatividade de locatário durante uma atualização do host, o recurso de domínio de atualização no Azure Stack existe somente para compatibilidade de modelo com o Azure.

## <a name="azure-stack-resiliency-resources"></a>Recursos de resiliência do Azure Stack
Para permitir o patch e atualização do Azure da pilha de um sistema integrado, e para ser resiliente a falhas de hardware físico, uma parte da memória total do servidor é reservado e não está disponível para posicionamento de máquina virtual (VM) do locatário.

Se um servidor falhar, as VMs hospedadas no servidor com falha serão reiniciadas nos servidores restantes, disponíveis para fornecer disponibilidade da VM. Da mesma forma, durante o processo de patch e atualização, todas as VMs em execução em um servidor ser operará migrado para outro servidor disponível. Esse gerenciamento de VM ou movimentação só pode ser obtida se não houver capacidade reservada para permitir a migração ou reinicialização ocorra.

O seguinte cálculo resulta na memória total e disponível que pode ser usada para o posicionamento de VM de locatário. É a capacidade de memória para a totalidade de unidade de escala do Azure Stack.

  Memória disponível para posicionamento de VM = sobrecarga da infraestrutura – reserva da resiliência – Total de memória de servidor do Azure Stack <sup>1</sup>

  Reserva de resiliência = H + R * (n-1) + V * (n-2)

> Em que:
> - H = tamanho da memória de servidor único
> - N = tamanho da unidade de escala (número de servidores)
> - R = reserva do sistema operacional para a sobrecarga do sistema operacional<sup>2</sup>
> - V = maior VM na unidade de escala

  <sup>1</sup> infraestrutura do azure Stack sobrecarga = 208 GB

  <sup>2</sup> reserva do sistema operacional para a sobrecarga = 15% da memória do nó. O valor de reserva do sistema operacional é uma estimativa e irão variar com base na capacidade de memória física do servidor e do sistema operacional geral de sobrecarga.

O valor V, maior VM na unidade de escala, baseia-se dinamicamente no maior tamanho de memória da VM de locatário. Por exemplo, o maior valor VM pode ser 7 GB ou 112 GB ou qualquer outro memória tamanho de VM suportado na solução do Azure Stack.

O cálculo acima é uma estimativa e estão sujeitas a mudanças com base na versão atual do Azure Stack. Capacidade de implantar serviços e VMs do locatário se baseia nas particularidades da solução implantada. Este exemplo de cálculo é apenas um guia e não é a resposta absoluta da capacidade de implantar VMs.



## <a name="next-steps"></a>Próximas etapas
[Planejamento de capacidade de armazenamento](capacity-planning-storage.md)
