---
title: "Sobre Migrações para Azure | Microsoft Docs"
description: "Fornece uma visão geral do serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: 393f2dfa29b930622a37b8ad90ee17b794c70aeb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

O serviço Migrações para Azure avalia as cargas de trabalho locais para migração para o Azure. O serviço avalia a adequação da migração e o dimensionamento com base no desempenho e fornece estimativas de custo para a execução das máquinas locais no Azure. Se está considerando as migrações lift-and-shift ou se está nos primeiros estágios da migração, este serviço é para você. Após a avaliação, você pode usar serviços como o Azure Site Recovery e a Migração de Banco de Dados do Azure para migrar as máquinas para o Azure.

> [!NOTE]
> A Migrações para Azure está atualmente em versão prévia e dá suporte a cargas de trabalho de produção.

## <a name="why-use-azure-migrate"></a>Por que usar Migrações para Azure?

As Migrações para Azure ajudam você a:

- **Avaliar a preparação para o Azure**: avalie se seus computadores locais são adequados para execução no Azure. 
- **Obter recomendações de tamanho**: obtenha as recomendações de tamanho para VMs do Azure após a migração, com base no histórico de desempenho das VMs locais. 
- **Estimar os custos mensais**: obtenha os custos estimados para execução de máquinas locais no Azure.  
- **Migrar com confiança alta**: visualize as dependências de computadores locais para criar grupos de computadores que serão avaliados e migrados juntos. Você pode exibir de forma precisa as dependências de uma máquina específica ou de todas as máquinas em um grupo.

## <a name="current-limitations"></a>Limitações atuais

- No momento, você pode avaliar as VMs (Máquinas Virtuais) VMware locais para migração para VMs do Azure.

> [!NOTE]
> O suporte para Hyper-V está em nossos planos e será habilitado em breve. Enquanto isso, recomendamos que você use o [Planejador de Implantações do Azure Site Recovery](http://aka.ms/asr-dp-hyperv-doc) para planejar a migração de cargas de trabalho do Hyper-V. 

- Você pode descobrir até 1.000 VMs em uma única descoberta e até 1.500 VMs em um único projeto. Além disso, você pode avaliar até 400 VMs em uma única avaliação. Se você precisar descobrir ou avaliar mais, poderá aumentar o número de descobertas ou avaliações. [Saiba mais](how-to-scale-assessment.md).
- A VM que você deseja avaliar deve ser gerenciada por um vCenter Server, versão 5.5, 6.0 ou 6.5.
- Você só pode criar um projeto de Migrações para Azure na região Centro-oeste dos EUA. No entanto, isso não afeta sua capacidade de planejar a migração para um local de destino diferente do Azure. O local do projeto de migração é usado apenas para armazenar os metadados descobertos no ambiente local.
- As Migrações para Azure são compatíveis com discos gerenciados apenas para avaliação de migração.

## <a name="what-do-i-need-to-pay-for"></a>Pelo que eu preciso pagar?

Saiba mais sobre os preços de Migrações para Azure [aqui](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

Uma avaliação ajuda a identificar a adequação do Azure de VMs locais, a obter recomendações de dimensionamento correto e estimativas de custo de execução das VMs no Azure. As avaliações se baseiam nas propriedades resumidas na tabela a seguir. Você pode modificar essas propriedades no portal de Migrações para Azure. 

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar. Por padrão, o local de destino é definido como Oeste dos EUA 2. 
**Redundância de armazenamento** | O tipo de armazenamento que as VMs do Azure usarão após a migração. LRS é o padrão.
**Planos de preço** | A avaliação considera se você está inscrito na garantia de software e se pode usar o [Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Também considera as ofertas do Azure que devem ser aplicadas e permite que você especifique descontos (%) específicos à assinatura, que você obtém sobre a oferta. 
**Tipo de preços** | Você pode especificar o [tipo de preço (básico/standard)](../virtual-machines/windows/sizes-general.md) das VMs do Azure. Isso o ajuda a migrar para uma família de VMs do Azure adequada, caso você esteja em um ambiente de produção. Por padrão o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
**Histórico de desempenho** | Por padrão, as Migrações para Azure avaliam o desempenho das máquinas locais usando um mês de histórico, com um valor de percentil de 95%. Você pode modificar essa configuração.
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
|Coletor          |vCenter Server          |Padrão 9443   | Por padrão, o coletor se conecta ao servidor vCenter na porta 9443. Se o servidor escutar em uma porta diferente, ela deverá ser configurada como uma porta de saída na VM coletora. |
|VM local     | Espaço de trabalho do Operations Management Suite (OMS)          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |O agente MMA usa TCP 443 para se conectar ao Log Analytics. Você só precisará dessa porta se estiver usando o recurso de visualização de dependência e estiver instalando o MMA (Microsoft Monitoring Agent). |


  
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