---
title: "Cálculos de avaliação em Migrações para Azure | Microsoft Docs"
description: "Fornece uma visão geral dos cálculos de avaliação no serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: db09ff30ff9f3852e84162b8400572e76515230f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

As [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure. Este artigo fornece informações sobre como as avaliações são calculadas.


## <a name="overview"></a>Visão geral

Uma avaliação das Migrações para Azure tem três etapas. A avaliação começa com uma análise de adequação, seguida de dimensionamento, e por fim, uma estimativa de custo mensal. Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se um computador falhar na verificação de adequação do Azure, será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão calculados. 


## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

Nem todos os computadores são adequados para executar em nuvem, já que a nuvem tem suas próprias limitações e requisitos. As Migrações para Azure avaliam cada computador local para adequação de migração para o Azure e categoriza os computadores em uma das seguintes categorias:
- **Pronto para o Azure** - O computador pode ser migrado como está para o Azure sem alterações. Ele será inicializado no Azure com o suporte completo do Azure.
- **Condicionalmente pronto para o Azure** - O computador pode ser inicializado no Azure, mas pode não ter suporte completo do Azure. Por exemplo, um computador com uma versão mais antiga do sistema operacional Windows Server não tem suporte no Azure. Você precisa ter cuidado antes de migrar esses computadores para o Azure e seguir as orientações de correção sugeridas na avaliação para corrigir os problemas de preparação antes de migrar.
- **Não está pronto para o Azure** - O computador não será inicializado no Azure. Por exemplo, se um computador local tem um disco com tamanho de mais de 4 TB anexado a ele, não poderá ser hospedado no Azure. Você precisa seguir as diretrizes de correção sugeridas na avaliação para corrigir o problema de preparação antes de migrar para o Azure. O dimensionamento correto e a estimativa de custo não são feitos para computadores marcados como não prontos para o Azure.
- **Preparação desconhecida** - As Migrações para Azure não puderam encontrar a preparação do computador devido a dados insuficientes disponíveis no vCenter Server. 

As Migrações para Azure revisam as propriedades do computador e o sistema operacional convidado para identificar a preparação do Azure do computador local.

### <a name="machine-properties"></a>Propriedades do computador
As Migrações para Azure analisam as seguintes propriedades da VM local para identificar se uma VM pode ser executada no Azure.
 
**Propriedade** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
**Tempo de inicialização** | O Azure oferece suporte a VMs com tipo de inicialização BIOS, e não UEFI. | Condicionalmente pronta para o Azure se o tipo de inicialização for UEFI. 
**Núcleos** | O número de núcleos em máquinas deve ser igual ou menor que o número máximo de núcleos (32) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as Migrações para Azure usarão os núcleos alocados, sem aplicar o fator de conforto. | Não pronto se o número de núcleos for maior do que 32. 
**Memória** | O tamanho da memória do computador deve ser igual ou menor que a memória máxima permitida para uma VM do Azure (448 GB). <br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória alocada será usada, sem aplicar o fator de conforto.<br/><br/> | Não pronto se o tamanho da memória for maior que 448 GB.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos anexados à máquina deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Não pronto se algum disco tiver tamanho maior que 4 TB ou se houver mais de 65 discos anexadas ao computador. 
**Rede** | Um computador deve ter 32 NICs ou menos conectados a ele. | Não pronto se a máquina tiver mais de 32 NICs

### <a name="guest-operating-system"></a>Sistema operacional convidado 
Junto com as propriedades da VM, as Migrações para Azure também examinam o sistema operacional convidado da VM local para identificar se a VM pode ser executada no Azure.

> [!NOTE]
> As Migrações para Azure consideram o sistema operacional especificado no vCenter Server para executar a análise a seguir. Como a descoberta feita pelas Migrações para Azure tem como base o dispositivo, ela não tem uma maneira de verificar se o sistema operacional em execução dentro da VM é o mesmo especificado no vCenter Server.

A lógica a seguir é usada pelas Migrações para Azure para identificar a preparação do Azure da VM com base no sistema operacional.

**Sistema operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte total.| Pronto para o Azure
Windows Server 2003-2008 R2 | Esses sistemas operacionais passaram da data final de suporte e precisam de um [Contrato de Suporte Personalizado (CSA)](https://aka.ms/WSosstatement) para obter suporte no Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Esses sistemas operacionais passaram da data final de suporte, o computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Cliente do Windows 7, 8 e 10 | O Azure fornece suporte apenas com a assinatura do Visual Studio. | Condicionalmente pronta para o Azure
Windows Vista, XP Professional | Esses sistemas operacionais passaram da data final de suporte, o computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais podem ser inicializados no Azure, mas é recomendado atualizar o sistema operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não é aprovada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, sistema operacional Apple Mac etc., FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. O computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável atualizar o sistema operacional antes de migrar para o Azure.  
Sistema operacional especificado como *Outros* no vCenter Server | As Migrações para Azure não podem identificar o sistema operacional neste caso. | Preparação desconhecida. Certifique-se de que o sistema operacional em execução dentro da VM tem suporte no Azure. 
Sistemas operacionais de 32 bits | O computador pode ser inicializado no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional do computador de 32 bits para 64 bits antes de migrar para o Azure.

## <a name="sizing"></a>Dimensionamento

Depois que uma máquina é marcada como pronta para o Azure, as Migrações para Azure dimensionam a VM e seus discos para o Azure. Se o critério de dimensionamento especificado nas propriedades de avaliação é para fazer o dimensionamento com base no desempenho, as Migrações para Azure consideram o histórico de desempenho do computador para identificar um tamanho de VM no Azure. Esse método é útil em cenários onde você alocou em excesso a VM local, mas a utilização é baixa e você deseja redimensionar as VMs no Azure para salvar o custo.

> [!NOTE]
> As Migrações para Azure coletam o histórico de desempenho de máquinas virtuais locais do vCenter Server. Para garantir um redimensionamento correto, verifique se a configuração de estatísticas no vCenter Server está definida para o nível 3 e aguarde pelo menos um dia antes de iniciar a descoberta de máquinas virtuais locais. Se a configuração de estatísticas no vCenter Server for menor que o nível 3, os dados de desempenho de disco e de rede não são coletados. 

Se você não deseja considerar o histórico de desempenho e deseja levar a VM como está para o Azure, você pode especificar o critério de dimensionamento como *como local* e as Migrações para Azure dimensionarão as VMs com base na configuração local sem considerar os dados de utilização.

### <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Para dimensionamento com base no desempenho, as Migrações para Azure iniciam com os discos anexados à VM, seguidas de adaptadores de rede e, em seguida, mapeiam uma VM do Azure com base nos requisitos de computação da VM local. 
 
- **Discos**: as Migrações para Azure tentam mapear todos os discos anexados à máquina para um disco no Azure. 
    
    > [!NOTE]
    > As Migrações para Azure oferecem suporte somente a discos gerenciados para avaliação.
    
    - Para obter a E/S de disco por segundo (IOPS) e a taxa de transferência (MBps) efetivas, as Migrações para Azure multiplicam o IOPS de disco e a taxa de transferência com o fator de conforto. Com base nos valores eficazes da taxa de transferência e IOPS, as Migrações para Azure identificam se o disco deve ser mapeado para um disco padrão ou premium no Azure.
    - Se as Migrações para Azure não conseguirem encontrar um disco com a IOPS e a taxa de transferência necessárias, ele marcará a máquina como inadequada para o Azure. [Saiba mais](../azure-subscription-service-limits.md#storage-limits) sobre os limites do Azure por disco e VM.
    - Se ele encontrar um conjunto de discos adequados, as Migrações para Azure selecionarão aqueles que dão suporte ao método de redundância de armazenamento e ao local especificado nas configurações de avaliação.
    - Se houver vários discos qualificados, elas selecionarão os que têm o menor custo.
    - Se os dados de desempenho de discos estiverem indisponíveis, todos os discos serão mapeados para os discos padrão no Azure.

- **Adaptadores de rede**: As Migrações para Azure tentam localizar uma VM do Azure que pode dar suporte ao número de adaptadores de rede conectados ao computador local e ao desempenho exigido por esses adaptadores de rede.
    - Para obter o desempenho de rede efetivo da VM local, as Migrações para Azure agregam os dados transmitidos por segundo (MBps) do computador (saída da rede), em todos os adaptadores de rede, e aplicam o fator de conforto. Esse número é usado para localizar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
    - Juntamente com o desempenho da rede, ele também considera se a VM do Azure pode dar suporte ao número necessário de adaptadores de rede.
    - Se nenhum dado de desempenho de rede estiver disponível, somente a contagem de adaptadores de rede será considerada para o dimensionamento da VM.

- **Tamanho da VM**: Depois que os requisitos de armazenamento e rede são calculados, as Migrações para Azure consideram os requisitos de CPU e memória para localizar um tamanho adequado de VM no Azure.
    - As Migrações para Azure analisam os núcleos e a memória utilizados e aplica o fator de conforto para obter os núcleos e memória efetivos. Com base nesse número, tentará encontrar um tamanho adequado de VM no Azure.
    - Se nenhum tamanho adequado for encontrado, a máquina será marcada como inadequada para o Azure.
    - Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Depois, aplicam as configurações de localização e preços, para a recomendação final de tamanho da VM.
    - Se houver vários discos qualificados, será recomendado aquele com o menor custo.

### <a name="as-on-premises-sizing"></a>Como dimensionamento local
Se o critério de dimensionamento é  *dimensionamento como local*, as Migrações para Azure não consideram o histórico de desempenho das VMs e alocam VMs e discos de acordo com o tamanho alocado no local.
- **Armazenamento**: Para cada disco, um disco padrão no Azure é recomendado com o mesmo tamanho que o disco local.
- **Rede**: Para cada adaptador de rede, é recomendado um adaptador de rede no Azure.
- **Computação**: As Migrações para Azure analisam o número de núcleos e o tamanho da memória da VM local e recomenda uma VM do Azure com a mesma configuração. Se houver vários discos qualificados, será recomendado aquele com o menor custo.
 
### <a name="confidence-rating"></a>Classificação de confiança

Cada avaliação das Migrações para Azure está associada a uma classificação de confiança que varia de 1 a 5 estrelas (1 estrela sendo a mais baixa e 5 estrelas sendo a mais alta). A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação. Isso ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure. 

A classificação de confiança é útil quando você estiver fazendo *dimensionamento com base no desempenho*, uma vez que nem todos os pontos de dados podem estar disponíveis. Para *dimensionamento como local*, a classificação de confiança é sempre de 5 estrelas, uma vez que as Migrações para Azure têm todos os dados necessários para dimensionar a VM. 

Para o dimensionamento com base no desempenho, as Migrações para Azure precisam de dados de utilização da CPU e memória. Para cada disco anexado à VM, é necessário ter a IOPS de leitura/gravação e a taxa de transferência para fazer o dimensionamento com base no desempenho. Da mesma forma, para cada adaptador de rede conectado à VM, as Migrações para Azure precisam da rede de entrada/saída para fazer o dimensionamento com base no desempenho. Se qualquer um dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelas Migrações para Azure pode não ser confiável. Dependendo do percentual dos pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a um dos seguintes motivos:
- A configuração de estatísticas no vCenter Server não está definida para o nível 3, e a avaliação tem o dimensionamento com base no desempenho como o critério de dimensionamento. Se a configuração de estatísticas no vCenter Server for menor que o nível 3, os dados de desempenho de disco e de rede não são coletados do vCenter Server. Nesse caso, a recomendação fornecida pelas Migrações para Azure para o disco e a rede tem como base somente o que foi alocado no local. Para o armazenamento, as Migrações para Azure recomendam discos padrão, pois não há como identificar se o disco tem IOPS/taxa de transferência altas e necessita de discos premium.
- A configuração de estatísticas no vCenter Server foi definida para o nível 3 para uma duração curta, antes de iniciar a descoberta. Por exemplo, se você alterar o nível de configuração de estatísticas para 3 hoje e iniciar a descoberta usando o dispositivo de coletor amanhã (após 24 horas), se estiver criando uma avaliação de um dia, você tem todos os pontos de dados. Mas se você estiver alterando a duração de desempenho nas propriedades de avaliação para um mês, a classificação de confiança diminui uma vez que o disco e os dados de desempenho de rede do último mês não estão disponíveis. Se você desejar considerar os dados de desempenho do último mês, é recomendável manter a configuração de estatísticas do vCenter Server no nível 3 por um mês antes de iniciar a descoberta. 
- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se todas as VMs foram desligadas por algum tempo, o vCenter Server não terá os dados de desempenho para aquele período. 
- Algumas VMs foram criadas durante o período para o qual a avaliação é calculada. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesses casos, o histórico de desempenho das novas VMs não estará lá durante todo o período.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de 3 estrelas, é recomendável alterar o nível de configurações de estatísticas do vCenter Server para 3, aguardar o tempo da duração que deseja avaliar (1 dia/1 semana/1 mês) e, em seguida, realizar a descoberta e a avaliação. Se não for possível fazer isso, o dimensionamento com base no desempenho pode não ser confiável, e é recomendável alternar para *dimensionamento como local* alterando as propriedades de avaliação.

## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Após a conclusão das recomendações de dimensionamento, as Migrações para Azure calcularão os custos de computação e armazenamento pós-migração.

- **Custo de computação**: usando o tamanho recomendado de VM do Azure, as Migrações para Azure usam a API de Cobrança para calcular o custo mensal para a VM. O cálculo leva em conta o sistema operacional, o software assurance, a localização e as configurações de moeda. Ele agrega o custo em todas as máquinas, para calcular o custo total de computação mensal.
- **Custo de armazenamento**: o custo de armazenamento mensal de uma máquina é calculado agregando o custo mensal de todos os discos anexados à máquina. As Migrações para Azure calculam o custo total de armazenamento mensal agregando os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não leva em consideração as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação. 


## <a name="next-steps"></a>Próximas etapas

[Criar uma avaliação para VMs VMware locais](tutorial-assessment-vmware.md)
