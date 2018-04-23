---
title: Sobre Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 03/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 283a505f9e16b081e0eb2e51d935c9b12e8721cb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

O serviço Migrações para Azure avalia as cargas de trabalho locais para migração para o Azure. O serviço avalia a adequação da migração de máquinas locais, o dimensionamento com base no desempenho e fornece estimativas de custo para a execução das máquinas locais no Azure. Se está considerando as migrações lift-and-shift ou se está nos primeiros estágios da migração, este serviço é para você. Após a avaliação, você pode usar serviços como o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview) para migrar as máquinas para o Azure.

## <a name="why-use-azure-migrate"></a>Por que usar Migrações para Azure?

As Migrações para Azure ajudam você a:

- **Avaliar a preparação para o Azure**: avalie se seus computadores locais são adequados para execução no Azure. 
- **Obter recomendações de tamanho**: obtenha as recomendações de tamanho para VMs do Azure após a migração, com base no histórico de desempenho das VMs locais. 
- **Estimar os custos mensais**: obtenha os custos estimados para execução de máquinas locais no Azure.  
- **Migrar com confiança alta**: visualize as dependências de computadores locais para criar grupos de computadores que serão avaliados e migrados juntos. 

## <a name="current-limitations"></a>Limitações atuais

- No momento, só é possível avaliar as máquinas virtuais (VMs) VMware locais para migração para VMs do Azure. As VMs VMware devem ser gerenciadas por um vCenter Server (versão 5.5, 6.0 ou 6.5).
- O suporte para Hyper-V está em nosso roteiro. Enquanto isso, recomendamos que você use o [Planejador de Implantações do Azure Site Recovery](http://aka.ms/asr-dp-hyperv-doc) para planejar a migração de cargas de trabalho do Hyper-V. 
- Você pode encontrar até 1.500 VMs em uma única descoberta e até 1.500 VMs em um único projeto. Além disso, pode estimar até 1.500 VMs em uma única avaliação.
- Você só pode criar um projeto de Migrações para Azure nas regiões Centro-Oeste ou Leste dos EUA. No entanto, isso não afeta sua capacidade de planejar a migração para um local de destino diferente do Azure. O local do projeto de migração é usado apenas para armazenar os metadados descobertos no ambiente local.
- As Migrações para Azure são compatíveis com discos gerenciados apenas para avaliação de migração.


## <a name="what-do-i-need-to-pay-for"></a>Pelo que eu preciso pagar?

Saiba mais sobre os preços de Migrações para Azure [aqui](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

Uma avaliação ajuda a identificar a adequação do Azure de VMs locais, a obter recomendações de dimensionamento correto e estimativas de custo de execução das VMs no Azure. Avaliações podem ser personalizadas com base em suas necessidades, alterando as propriedades da avaliação. Abaixo, estão as propriedades que são consideradas durante a criação de uma avaliação. 

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar.<br/><br/>O Migrações para Azure atualmente dá suporte a 30 regiões, incluindo Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Canadá Central, Leste do Canadá, Índia Central, Centro dos EUA, Leste da China, Norte da China, Ásia Oriental, Leste dos EUA, Alemanha Central, Nordeste da Alemanha, Leste dos EUA 2, Leste do Japão, Oeste do Japão, Coreia do Sul, Centro Sul, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, Centro-Oeste dos EUA, Europa Ocidental, Oeste da Índia, Oeste dos EUA e Oeste dos EUA 2. Por padrão, o local de destino é definido como Oeste dos EUA 2. 
**Redundância de armazenamento** | O tipo de [redundância de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-redundancy) que as VMs do Azure usarão após a migração. O armazenamento com redundância local (LRS) é o padrão. Observe que as Migrações para Azure só oferecem suporte a avaliações baseadas em discos gerenciados, e discos gerenciados só oferecem suporte ao LRS, portanto, a propriedade só tem a opção de LRS atualmente. 
**Critério de dimensionamento** | O critério a ser usado pelas Migrações para Azure para VMs do tamanho certo para o Azure. É possível fazer o dimensionamento com base em *histórico de desempenho* das VMs locais ou dimensionar as VMs *como locais* para o Azure sem considerar o histórico de desempenho. O valor padrão é o dimensionamento baseado em desempenho.
**Planos de preço** | Para cálculos de custo, uma avaliação leva em consideração se você tem software assurance e se está qualificados para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Também leva em consideração as [Ofertas do Azure](https://azure.microsoft.com/support/legal/offer-details/) às quais você deve se inscrever, além de permitir que você especifique quaisquer descontos (%) específicos à assinatura, que você pode obter com a oferta. 
**Tipo de preços** | É possível especificar o [tipo de preço (Básico/Standard)](../virtual-machines/windows/sizes-general.md) das VMs de destino do Azure. Por exemplo, se você estiver planejando migrar um ambiente de produção, deve considerar a camada Standard, que fornece VMs com baixa latência, mas pode custar mais. Por outro lado, caso tenha um ambiente de desenvolvimento de teste, talvez você deva considerar a camada Básica, que tem VMs com latência maior e custos mais baixos. Por padrão, o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
**Histórico de desempenho** | Aplicável somente se o critério de dimensionamento for baseado em desempenho. Por padrão, as Migrações para Azure avaliam o desempenho das máquinas locais usando o histórico de desempenho do último dia, com um valor de percentil de 95%. É possível modificar esses valores nas propriedades de avaliação. 
**Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. A configuração de conforto padrão é de 1.3 x.


## <a name="how-does-azure-migrate-work"></a>Como funcionam as Migrações para Azure?

1.  Você cria um projeto das Migrações para Azure.
2.  As Migrações para Azure usam uma VM local chamada dispositivo coletor para descobrir informações sobre suas máquinas locais. Para criar o dispositivo, baixe o arquivo de configuração no formato Open Virtualization Appliance (.ova) e importe-o como uma VM em seu servidor local do vCenter Server.
3.  Você se conecta à VM usando a conexão de console no vCenter Server, especifica uma nova senha para a máquina virtual durante a conexão e, em seguida, executa o aplicativo coletor na VM para iniciar a descoberta.
4.  O coletor coleta metadados da VM usando os cmdlets de VMware PowerCLI. A descoberta não tem agente e não instala nada em VMs ou hosts VMware. Os metadados coletados incluem informações da VM (núcleos, memória, discos, tamanhos de disco e adaptadores de rede). Também coleta dados de desempenho para VMs, incluindo CPU e uso da memória, IOPS de disco, taxa de transferência do disco (MBps) e saída da rede (MBps).
5.  Os metadados são enviados para o projeto das Migrações para Azure. Consulte-os no Portal do Azure.
6.  Para fins de avaliação, junte as VMs descobertas em grupos. Por exemplo, agrupe as VMs que executam o mesmo aplicativo. Para um agrupamento mais preciso, você pode usar a visualização de dependência para exibir as dependências de um computador específico ou para todos os computadores em um grupo e refinar o grupo.
7.  Depois que o grupo for formado, você poderá criar uma avaliação para o grupo. 
8.  Após a conclusão da avaliação, você poderá exibi-la no portal ou baixá-la no formato do Excel.



  ![Arquitetura do Planejador do Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Quais são os requisitos de porta?

A tabela resume as portas necessárias para as comunicações das Migrações para Azure.

|Componente          |Para comunicar-se com     |Porta obrigatória  |Motivo   |
|-------------------|------------------------|---------------|---------|
|Coletor          |Serviço Migrações para Azure   |TCP 443        |O coletor se conecta ao serviço através da porta SSL 443|
|Coletor          |vCenter Server          |Padrão 9443   | Por padrão, o coletor se conecta ao vCenter Server na porta 9443. Se o servidor escutar em uma porta diferente, ela deverá ser configurada como uma porta de saída na VM coletora. |
|VM local     | Espaço de trabalho do Log Analytics          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |O agente MMA usa TCP 443 para se conectar ao Log Analytics. Você só precisará dessa porta se estiver usando o recurso de visualização de dependência e estiver instalando o MMA (Microsoft Monitoring Agent). |


  
## <a name="what-happens-after-assessment"></a>O que acontece após a avaliação?

Após a avaliação das máquinas locais para migração com o serviço de Migrações para Azure, use algumas das ferramentas para executar a migração:

- **Azure Site Recovery**: você pode usar o Azure Site Recovery para migrar para o Azure, da seguinte maneira:
  - Prepare os recursos do Azure, incluindo uma assinatura do Azure, uma rede virtual do Azure e uma conta de armazenamento.
  - Prepare os servidores VMware locais para migração. Verifique os requisitos de suporte do VMware para o Site Recovery, prepare os servidores VMware para descoberta, e prepare-se para instalar o serviço de Mobilidade do Site Recovery em VMs que você deseja migrar. 
  - Configure a migração. Configure um cofre dos Serviços de Recuperação, defina as configurações de migração de origem e destino, configure uma política de replicação e habilite a replicação. Execute uma simulação de recuperação de desastres para verificar se a migração de uma VM do Azure está funcionando corretamente.
  - Execute um failover para migrar máquinas locais para o Azure. 
  - [Saiba mais](../site-recovery/tutorial-migrate-on-premises-to-azure.md) no tutorial de migração do Site Recovery.

- **Migração de Banco de Dados do Azure**: se as máquinas locais estiverem executando um banco de dados como SQL Server, MySQL ou Oracle, use o Serviço de Migração de Banco de Dados do Azure para migrá-los para o Azure. [Saiba mais](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>Próximas etapas 
[Siga um tutorial](tutorial-assessment-vmware.md) para criar uma avaliação para um VM local do VMware.