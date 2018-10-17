---
title: Sobre Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/25/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d2a8885ffb9148d408eff0e8a7d2ef09121e5359
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162025"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

O serviço Migrações para Azure avalia as cargas de trabalho locais para migração para o Azure. O serviço avalia a adequação da migração de máquinas locais, realiza o dimensionamento com base no desempenho e fornece estimativas de custo para a execução das máquinas locais no Azure. Se está considerando as migrações lift-and-shift ou se está nos primeiros estágios da migração, este serviço é para você. Após a avaliação, você pode usar serviços como o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview) para migrar as máquinas para o Azure.

## <a name="why-use-azure-migrate"></a>Por que usar Migrações para Azure?

As Migrações para Azure ajudam você a:

- **Avaliar a preparação para o Azure**: avalie se seus computadores locais são adequados para execução no Azure.
- **Obter recomendações de tamanho**: obtenha as recomendações de tamanho para VMs do Azure após a migração, com base no histórico de desempenho das VMs locais.
- **Estimar os custos mensais**: obtenha os custos estimados para execução de máquinas locais no Azure.  
- **Migrar com confiança alta**: visualize as dependências de computadores locais para criar grupos de computadores que serão avaliados e migrados juntos.

## <a name="current-limitations"></a>Limitações atuais

- No momento, só é possível avaliar as máquinas virtuais (VMs) VMware locais para migração para VMs do Azure. As VMs VMware devem ser gerenciadas por um vCenter Server (versão 5.5, 6.0 ou 6.5).
- Se você quer avaliar as VMs Hyper-V e servidores físicos, use o [Planejador de Implantações do Azure Site Recovery](http://aka.ms/asr-dp-hyperv-doc) para Hyper-V e nossas [ferramentas de parceiros](https://azure.microsoft.com/migration/partners/) para computadores físicos.
- Você pode encontrar até 1.500 VMs em uma única descoberta e até 1.500 VMs em um único projeto. Além disso, pode estimar até 1.500 VMs em uma única avaliação.
- Caso deseje descobrir um ambiente maior, você pode dividir a descoberta e criar vários projetos. [Saiba mais](how-to-scale-assessment.md). As Migrações para Azure oferecem suporte a até 20 projetos por assinatura.
- As Migrações para Azure são compatíveis com discos gerenciados apenas para avaliação de migração.
- Você só pode criar um projeto das Migrações para Azure na região geográfica dos Estados Unidos. Isso não afeta sua capacidade de planejar a migração para qualquer local do Azure de destino. A geografia do projeto de migração é usado apenas para armazenar os metadados descobertos no ambiente local. [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#discovery-and-assessment) sobre os metadados coletados pelas Migrações para Azure. Os metadados coletados são armazenados em uma das regiões da [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) selecionada:

**Geografia** | **Regiões**
--- | ---
Estados Unidos | Centro-oeste dos EUA, Leste dos EUA

## <a name="what-do-i-need-to-pay-for"></a>Pelo que eu preciso pagar?

[Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.


## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

As configurações de avaliação podem ser personalizadas de acordo com suas necessidades. As propriedades de avaliação estão resumidas na tabela a seguir.

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar.<br/><br/>As Migrações para Azure atualmente dão suporte a 30 regiões. [Verificar regiões](https://azure.microsoft.com/global-infrastructure/services/). Por padrão, a região de destino é definida como Oeste dos EUA 2.
**Tipo de armazenamento** | o tipo dos discos que deseja alocar no Azure. Isso se aplica quando o critério de dimensionamento for **local**. Especifique o tipo de disco de destino como discos gerenciados Premium (o padrão) ou Standard. Para o dimensionamento com base em desempenho, a recomendação do dimensionamento do disco é feita automaticamente com base nos dados de desempenho das VMs.
**Critério de dimensionamento** | O dimensionamento pode ser baseado no **histórico de desempenho** das VMs locais ou **local** (padrão), sem considerar o histórico de desempenho.
**Oferta do Azure** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se inscreveu. As Migrações para Azure calculam o custo de acordo com isso.
**Benefício Híbrido do Azure** | Se você tem a garantia do software e está qualificado para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) com desconto nos valores.
**Instâncias reservadas** |  Se você tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure. As Migrações para Azure calculam o custo de acordo com isso.
**Tempo de atividade da VM** | A duração da execução das VMs no Azure. As estimativas de custo são feitas de acordo com isso.
**Tipo de preços** | O [tipo de preço (Básico/Standard)](../virtual-machines/windows/sizes-general.md) das VMs de destino do Azure. Por exemplo, se você estiver planejando migrar um ambiente de produção, deverá considerar a camada Standard, que fornece VMs com baixa latência, mas pode custar mais. Por outro lado, em um ambiente de teste, você poderia usar a camada básica com maior latência e custos mais baixos. Por padrão o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
**Histórico de desempenho** | Por padrão, as Migrações para Azure avaliam o desempenho das máquinas locais usando o histórico de desempenho do último dia, com um valor de percentil de 95%.
**Série de VM** | A série de VM usada para estimativas de tamanho. Por exemplo, se você tiver um ambiente de produção que não pretende migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série. O dimensionamento baseia-se apenas na série selecionada.   
**Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. A configuração de conforto padrão é de 1.3 x.


## <a name="how-does-azure-migrate-work"></a>Como funcionam as Migrações para Azure?

1.  Você cria um projeto das Migrações para Azure.
2.  As Migrações para Azure usam uma VM local chamada dispositivo coletor para descobrir informações sobre suas máquinas locais. Para criar o dispositivo, baixe o arquivo de configuração no formato Open Virtualization Appliance (.ova) e importe-o como uma VM em seu servidor local do vCenter Server.
3. Você se conecta à VM do vCenter Server e especifica uma nova senha para ele durante a conexão.
4. Execute o coletor na VM para iniciar a descoberta.
5. O coletor coleta metadados da VM usando os cmdlets de VMware PowerCLI. A descoberta não tem agente e não instala nada em VMs ou hosts VMware. Os metadados coletados incluem informações da VM (núcleos, memória, discos, tamanhos de disco e adaptadores de rede). Também coleta dados de desempenho para VMs, incluindo CPU e uso da memória, IOPS de disco, taxa de transferência do disco (MBps) e saída da rede (MBps).
5.  Os metadados são enviados para o projeto das Migrações para Azure. Consulte-os no Portal do Azure.
6.  Para fins de avaliação, junte as VMs descobertas em grupos. Por exemplo, agrupe as VMs que executam o mesmo aplicativo. Para um agrupamento mais preciso, você pode usar a visualização de dependência para exibir as dependências de um computador específico ou para todos os computadores em um grupo e refinar o grupo.
7.  Após o grupo ser definido, crie uma avaliação para ele.
8.  Após a conclusão da avaliação, você poderá exibi-la no portal ou baixá-la no formato do Excel.

  ![Arquitetura das Migrações para Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Quais são os requisitos de porta?

A tabela resume as portas necessárias para as comunicações das Migrações para Azure.

Componente | Comunica-se com |  Detalhes
--- | --- |---
Coletor  | Serviço Migrações para Azure | O coletor se conecta ao serviço através da porta SSL 443.
Coletor | vCenter Server | Por padrão, o coletor se conecta ao vCenter Server na porta 443. Se o servidor escutar em uma porta diferente, configure-a como uma porta de saída na VM coletora.
VM local | Espaço de trabalho do Log Analytics | [TCP 443] | [O MMA (Microsoft Monitoring Agent)](../log-analytics/log-analytics-windows-agent.md) usa a porta TCP 443 para se conectar ao Log Analytics. Você só precisará dessa porta se estiver usando a visualização de dependência, que requer o agente MMA.


## <a name="what-happens-after-assessment"></a>O que acontece após a avaliação?

Após a avaliação das máquinas locais, você poderá usar algumas ferramentas para executar a migração:

- **Azure Site Recovery**: você pode usar o Azure Site Recovery para migrar para o Azure. Para fazer isso, [prepare os componentes do Azure](../site-recovery/tutorial-prepare-azure.md) necessários, incluindo uma conta de armazenamento e uma rede virtual. No local, [prepare seu ambiente VMware](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Quando tudo estiver preparado, configure e habilite a replicação para o Azure e migre as VMs. [Saiba mais](../site-recovery/vmware-azure-tutorial.md).
- **Migração de Banco de Dados do Azure**: se as máquinas locais estiverem executando um banco de dados como SQL Server, MySQL ou Oracle, use o [Serviço de Migração de Banco de Dados do Azure](../dms/dms-overview.md) para migrá-los para o Azure.


## <a name="next-steps"></a>Próximas etapas

- [Siga o tutorial](tutorial-assessment-vmware.md) para criar uma avaliação para uma VM VMware local.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
