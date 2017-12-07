---
title: "Cálculos de avaliação em Migrações para Azure | Microsoft Docs"
description: "Fornece uma visão geral dos cálculos de avaliação no serviço Migrações para Azure."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: f00825ff9a5018e67672ce452f01130f84919e52
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

As [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure. Este artigo fornece informações sobre como as avaliações são calculadas.



## <a name="overview"></a>Visão geral

Uma avaliação das Migrações para Azure tem três etapas. A avaliação começa com uma análise de adequação, seguida por estimativas de dimensionamento com base em desempenho e, por fim, uma estimativa de custo mensal. Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se uma máquina falhar na verificação de adequação do Azure, será marcada como não adequada para o Azure, e o dimensionamento e custo não serão calculados. 


## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

As máquinas que você deseja migrar para o Azure devem atender aos requisitos e limitações do Azure. Por exemplo, se o disco de uma VM local tiver mais de 4 TB, ele não poderá ser hospedado no Azure. As verificações de conformidade do Azure estão resumidas na tabela a seguir. 

**Verificação** | **Detalhes**
--- | ---
**Tempo de inicialização** | O tipo de inicialização do disco do SO convidado deve ser BIOS, e não UEFI.
**Núcleos** | O número de núcleos nas máquinas deve ser igual a (ou menor do que) o número máximo de núcleos (32) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as Migrações para Azure usarão os núcleos alocados, sem aplicar o fator de conforto.
**Memória** | O tamanho da memória da máquina deve ser igual a (ou menor do que) (448 GB) de memória máxima permitida para uma VM do Azure. <br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória alocada será usada, sem aplicar o fator de conforto.<br/><br/> 
**Windows Server 2003-2008 R2** | Suporte de 32 bits e 64 bits.<br/><br/> O Azure fornece somente o suporte mínimo.
**Windows Server 2008 R2 com todos os SPs** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte total.
**Windows Server 2012 e todos os SPs** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte total.
**Windows Server 2012 R2 e todo os SPs** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte total.
**Windows Server 2016 e todo os SPs** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte total.
**Cliente do Windows 7 e posterior** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte apenas com a assinatura do Visual Studio.
**Linux** | Suporte a 64 bits.<br/><br/> O Azure fornece suporte total a estes [sistemas operacionais](../virtual-machines/linux/endorsed-distros.md).
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos anexados à máquina deve ser de 65 ou menos, incluindo o disco do sistema operacional. 
**Rede** | Um computador deve ter 32 NICs ou menos conectados a ele.


## <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Após uma máquina ser indicada como adequada ao Azure, as Migrações para Azure a mapeiam para um tamanho de VM no Azure, usando os seguintes critérios:

- **Verificação de armazenamento**: as Migrações para Azure tentam mapear todo disco anexado à máquina para um disco no Azure: As Migrações para Azure multiplicam a IOPS (E/S por segundo) pelo fator de conforto. Também multiplicam a taxa de transferência (em MBps) de cada disco pelo fator de conforto. Isso fornece a IOPS efetiva do disco e a taxa de transferência. Com base nisso, as Migrações para Azure mapeiam o disco para um disco padrão ou premium no Azure.
    - Se o serviço não conseguir encontrar um disco com a IOPS e a taxa de transferência necessárias, ele marcará a máquina como inadequada para o Azure.
    - Se ele encontrar um conjunto de discos adequados, as Migrações para Azure selecionarão aqueles que dão suporte ao método de redundância de armazenamento e ao local especificado nas configurações de avaliação.
    - Se houver vários discos qualificados, elas selecionarão os que têm o menor custo.
- **Taxa de transferência de disco de armazenamento**: [Saiba mais](../azure-subscription-service-limits.md#storage-limits) sobre os limites do Azure por disco e VM.
- **Tipo de disco**: as Migrações para Azure oferecem suporte somente a discos gerenciados.
- **Verificação de rede**: as Migrações para Azure tentam localizar uma VM do Azure que possa dar suporte ao número de NICs na máquina local.
    - Para fazer isso, elas agregam os dados transmitidos por segundo (MBps) da máquina (rede) em todos os NICs e aplica o fator de conforto ao número agregado. Esse número é usado para localizar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
    - As Migrações para Azure usam as configurações de rede da VM e pressupõe que ela seja uma rede fora do data center.
    - Se nenhum dado de desempenho de rede estiver disponível, somente a contagem de NIC será considerada para o dimensionamento da VM.
- **Verificação de computação**: após o cálculo dos requisitos de armazenamento e de rede, as Migrações para Azure considerarão os requisitos de computação:
    - Se os dados de desempenho estiverem disponíveis para a VM, elas examinarão os núcleos e a memória utilizados e aplicará o fator de conforto. Com base nesse número, tentará encontrar um tamanho adequado de VM no Azure.
    - Se nenhum tamanho adequado for encontrado, a máquina será marcada como inadequada para o Azure.
    - Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Depois, aplicam as configurações de localização e preços, para a recomendação final de tamanho da VM.


## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Após a conclusão das recomendações de dimensionamento, as Migrações para Azure calcularão os custos de computação e armazenamento pós-migração.

- **Custo de computação**: usando o tamanho recomendado de VM do Azure, as Migrações para Azure usam a API de Cobrança para calcular o custo mensal para a VM. O cálculo leva em conta o sistema operacional, o software assurance, a localização e as configurações de moeda. Ele agrega o custo em todas as máquinas, para calcular o custo total de computação mensal.
- **Custo de armazenamento**: o custo de armazenamento mensal de uma máquina é calculado agregando o custo mensal de todos os discos anexados à máquina. As Migrações para Azure calculam o custo total de armazenamento mensal agregando os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não leva em consideração as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação. 


## <a name="next-steps"></a>Próximas etapas

[Configurar uma avaliação para VMs do VMware locais](tutorial-assessment-vmware.md)
