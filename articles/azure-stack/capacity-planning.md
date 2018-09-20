---
title: Planejamento de capacidade para o Azure Stack | Microsoft Docs
description: Saiba mais sobre o planejamento de capacidade para implantações do Azure Stack.
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
ms.openlocfilehash: ad76e19e324c29c277e72f1e93d2b505984b50ba
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368985"
---
# <a name="azure-stack-capacity-planning"></a>Planejamento de capacidade do Azure Stack
Ao avaliar uma solução de pilha do Azure, há opções de configuração de hardware que têm um impacto direto na capacidade geral da nuvem do Azure Stack. Essas são as opções clássicas da CPU, densidade de memória, configuração de armazenamento e geral escala da solução ou número de servidores. Ao contrário de uma solução de virtualização tradicionais, não é aplicável a aritmética simple desses componentes para determinar a capacidade utilizável. O primeiro motivo para isso é que o Azure Stack é projetado para hospedar os componentes de infraestrutura ou gerenciamento de dentro da solução em si. O segundo motivo é que alguns de capacidade da solução está reservado para dar suporte à resiliência; a atualização de software da solução de forma a minimizar as interrupções de cargas de trabalho de locatário.

> [!IMPORTANT]
> As informações de planejamento de capacidade fornecidos e planilha que acompanha este artigo, destinam-se apenas como um guia para ajudá-lo a tornar o planejamento do Azure Stack e decisões de configuração. Eles não se destina a servir como um substituto para a sua investigação e análise. 

## <a name="compute-and-storage-capacity-planning"></a>Computação e o planejamento de capacidade de armazenamento
Uma solução de pilha do Azure baseia-se como um cluster hiperconvergente de computação, rede e armazenamento. Isso permite o uso efetivo ou compartilhamento de toda a capacidade de hardware no cluster, conhecido como uma unidade de escala para o Azure Stack, de forma que fornece disponibilidade e escalabilidade. Todos os softwares de infraestrutura é hospedado dentro de um conjunto de máquinas virtuais (VMs) e compartilha os mesmos servidores físicos, como as VMs do locatário. Todas as máquinas virtuais, em seguida, são gerenciadas por tecnologias de cluster a unidade de escala Windows Server e instâncias individuais do Hyper-V. Essa abordagem simplifica a aquisição e gerenciamento de uma solução do Azure Stack e permite que para a movimentação e a escalabilidade de todos os serviços (locatário e infraestrutura) em todo o a unidade de escala.

O recurso somente físico que não está provisionado em excesso em uma solução do Azure Stack é a memória do servidor. Os outros recursos, da CPU núcleos, largura de banda de rede e capacidade de armazenamento, serão ser sobreprovisionados para fazer o melhor uso dos recursos disponíveis. Ao calcular a capacidade disponível para uma solução, a memória de servidor físico é o colaborador principal. A utilização de outros recursos é, em seguida, Noções básicas sobre a taxa de excesso de provisionamento que são possíveis e qual será aceitável para a carga de trabalho pretendida.

VMs de aproximadamente 28 são usadas para hospedar a infraestrutura do Azure Stack e, no total, consomem aproximadamente 208 GB de memória e núcleos virtuais 124.  A lógica para este número de VMs é para satisfazer a separação de serviço necessário para atender a segurança, escalabilidade, requisitos de aplicação de patch e manutenção. Essa estrutura de serviço interno permite que a futura introdução de novos serviços de infraestrutura conforme eles são desenvolvidos.

Para dar suporte a atualização automatizada de toda a infraestrutura de servidor físico e componentes de software de infraestrutura, ou o patch e atualização e o usuário VM posicionamentos não consumirá todos os recursos de memória da unidade de escala. Uma quantidade de memória total em todos os servidores de uma unidade de escala será alocada para dar suporte a requisitos de resiliência da solução. Por exemplo, quando a imagem do Windows Server do servidor físico for atualizada, as VMs hospedadas no servidor são movidas em outro lugar dentro da unidade de escala enquanto imagens do Windows Server do servidor está atualizada. Quando a atualização for concluída, o servidor é reiniciado e retornado para cargas de trabalho de manutenção. A meta para o patch e atualização de uma solução do Azure Stack é evitar a necessidade de parar VMs hospedadas. Em se esforçando para atingir essa meta, mínimo de capacidade de memória de um servidor não alocado para permitir a movimentação de máquinas virtuais dentro da unidade de escala. Esse posicionamento e a movimentação se aplica a VMs de infraestrutura e máquinas virtuais criadas em nome de usuário ou Locatário da solução do Azure Stack. Os resultados da implementação final fazem com que a quantidade de memória reservada para dar suporte a movimentação de VM necessária pode ser muito mais do que a capacidade de um único servidor devido a desafios de posicionamento quando as VMs têm diversos requisitos de memória. Uma sobrecarga adicional na categoria de uso de memória é que da própria instância do Windows Server. A instância do sistema operacional de base para cada servidor consumirão memória para o sistema operacional e suas tabelas de página virtual junto com a memória usada pelo Hyper-V no gerenciamento de cada uma das VMs hospedadas.

Uma descrição detalhada das complexidades de cálculos de capacidade é descrevem posteriormente nesta seção. Nesta introdução, os exemplos a seguir são fornecidos para ajudar a compreender a capacidade disponível de variados tamanhos de solução. Esses são estimativas e assim fazem suposições sobre o uso de memória VM que não pode ser verdadeiro para instalações de produção do locatário. Para essa tabela, o tamanho de VM do Azure de D2 padrão é usado. As VMs do Azure Standard D2 são definidas com 2 CPUs virtuais e 7 GB de memória.

|     |Por capacidade do servidor|| Capacidade da unidade de escala|  |  |||
|-----|-----|-----|-----|-----|-----|-----|-----|
|     | Memória | Núcleos de CPU | Número de servidores | Memória | Núcleos de CPU | Máquinas virtuais de locatários<sup>1</sup>     | Taxa de núcleo<sup>2</sup>    |
|Exemplo 1|256 GB|28|4|1024 GB| 112 | 54 |4:3|
|Exemplo 2|512 GB|28|4|2024 GB|112|144|4:1|
|Exemplo 3|384 GB|28|12|4.608 GB|336|432|3:1|
|     |     |     |     |     |     |     |     |

> <sup>1</sup> VMs padrão D2.

> <sup>2</sup> núcleo Virtual para a proporção de núcleos físicos.

Conforme mencionado acima, a capacidade VM é determinada pela memória disponível. Os-de núcleos virtuais para núcleos físicos taxas exemplificam como a densidade da VM mudará capacidade disponível da CPU, a menos que a solução é construída com um número maior de núcleos físicos (outra CPU é escolhida). O mesmo é verdadeiro para capacidade de armazenamento e capacidade de armazenamento de cache.

Os exemplos acima de densidade VM são para apenas para fins de explicação. Não há mais complexidade os cálculos de suporte. Contato com a Microsoft ou um parceiro de solução é necessário para compreender melhor as opções de planejamento de capacidade e a capacidade disponível, resultante.

O restante desta seção descreve os requisitos de implantação do Azure Stack para computação e armazenamento. Use essas informações para obter uma base de compreensão de quais componentes são necessários e seus valores de configuração mínima. Informações adicionais também são fornecidas para descrever como a solução é configurada com relação à capacidade disponível e os limites potenciais do sistema em relação à capacidade de capacidade e desempenho do locatário.

> [!NOTE]
> Os requisitos de planejamento de capacidade de rede são mínimos, pois somente o tamanho do VIP público é configurável. Para obter informações sobre como adicionar mais endereços IP públicos para o Azure Stack, consulte [adicionar os endereços IP públicos](azure-stack-add-ips.md).


## <a name="next-steps"></a>Próximas etapas
[Planejamento de capacidade de computação](capacity-planning-compute.md)
