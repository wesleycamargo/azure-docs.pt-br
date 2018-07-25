---
title: Visão geral da migração da Contoso para o Azure | Microsoft Docs
description: Fornece uma visão geral da estratégia e os cenários de migração usados pela Contoso para migrar seu datacenter local para o Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 4cad84d1cabd140f4846cb823e8c4b7ce11bc1f6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008239"
---
# <a name="contoso-migration-overview"></a>Migração da Contoso: visão geral


Este artigo demonstra como a organização fictícia Contoso migra uma infraestrutura local para a nuvem do [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/). 

Este documento é o primeiro em uma série de artigos que mostram como a empresa fictícia Contoso migra para o Azure. A série inclui informações e cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Os cenários crescem em complexidade e adicionaremos artigos adicionais ao longo do tempo. Os artigos mostram como a empresa Contoso conclui sua missão de migração, mas fornece ponteiros para leitura geral e instruções específicas.

## <a name="introduction"></a>Introdução

O Azure fornece acesso a um conjunto abrangente de serviços de nuvem. Como desenvolvedor e profissional de TI, você pode usar esses serviços para criar, implantar e gerenciar aplicativos em uma variedade de ferramentas e estruturas, por meio de uma rede global de datacenters. Conforme a sua empresa vai enfrentando desafios associados à mudança digital, a nuvem do Azure o ajuda a descobrir como otimizar recursos e operações, envolver-se com seus clientes e funcionários e transformar seus produtos.

No entanto, o Azure reconhece que mesmo com todas as vantagens que a nuvem oferece em termos de velocidade e flexibilidade, redução de custos, desempenho e confiabilidade, muitas organizações ainda precisarão executar datacenters locais por algum tempo. Em resposta aos obstáculos para a adoção da nuvem, o Azure oferece uma estratégia de nuvem híbrida que cria pontes entre seus datacenters locais e a nuvem pública do Azure. Por exemplo, o uso de recursos de nuvem do Azure, como backup para proteger recursos locais, ou o uso de análise do Azure para obter insights sobre cargas de trabalho locais. 

Como parte da estratégia de nuvem híbrida, o Azure fornece soluções de crescimento para migrar aplicativos locais e cargas de trabalho para a nuvem. Com etapas simples, você pode avaliar seus recursos locais de forma abrangente para descobrir como eles serão executados na nuvem do Azure. Com uma avaliação detalhada, você pode migrar seus recursos para o Azure com tranquilidade. Quando os recursos estão em execução no Azure, você pode otimizá-los para manter e melhorar o acesso, a flexibilidade, a segurança e a confiabilidade.

## <a name="migration-strategies"></a>Estratégias de migração

As estratégias para a migração para a nuvem se enquadram em quatro amplas categorias: hospedar novamente, refatorar, refazer arquitetura ou recompilar. A estratégia que você adotará depende de seus geradores de negócios e das metas de migração. Você pode adotar várias estratégias. Por exemplo, você pode optar por novo host aplicativos simples (lift-and-shift), ou aplicativos que não sejam críticos para sua empresa, mas refazer a arquitetura para aqueles que sejam mais complexos e essenciais. Vamos examinar as estratégias.


**Estratégia** | **Definição** | **Quando usar** 
--- | --- | --- 
**Hospedar novamente** | Conhecida como uma migração "lift-and-shift". Essa opção não exige alterações de código e permite que você migre seus aplicativos existentes para o Azure rapidamente. Cada aplicativo é migrado como está, para aproveitar os benefícios da nuvem, sem o risco e o custo associado com as alterações de código. | Quando você precisar mover aplicativos rapidamente para a nuvem.<br/><br/> Quando você deseja mover um aplicativo sem modificá-lo.<br/><br/> Quando os aplicativos são projetados para que eles podem aproveitar a escalabilidade do [IaaS do Azure](https://azure.microsoft.com/overview/what-is-iaas/) após a migração.<br/><br/> Quando aplicativos são importantes para a sua empresa, mas você não precisa de alterações imediatas nos recursos de aplicativo.
**Refatorar** | Conhecida como "reempacotamento", a refatoração requer alterações mínimas nos aplicativos, para que eles possam se conectar ao [PaaS do Azure](https://azure.microsoft.com/overview/what-is-paas/) e usar as ofertas de nuvem.<br/><br/> Por exemplo, você pode migrar seus aplicativos existentes para o Serviço de Aplicativo do Azure ou o AKS (Serviço Kubernetes do Azure).<br/><br/> Ou você pode refatorar seus bancos de dados relacionais e não relacionais em opções como a Instância Gerenciada do Banco de Dados SQL do Azure, o Banco de Dados do Azure para MySQL, o Banco de Dados do Azure para PostgreSQL e o Azure Cosmos DB. | Se seu aplicativo pode facilmente ser empacotado novamente para funcionar no Azure.<br/><br/> Se você deseja aplicar práticas DevOps inovadoras fornecidas pelo Azure, ou se estiver pensando em DevOps usando uma estratégia de contêiner para cargas de trabalho.<br/><br/> Para a refatoração, você precisa pensar sobre a portabilidade de sua base de código existente e as habilidades de desenvolvimento disponíveis.
**Recriação de arquitetura** | A nova arquitetura para migração se concentra em modificar e estender a funcionalidade do aplicativo e o código base para otimizar a arquitetura do aplicativo de escalabilidade de nuvem.<br/><br/> Por exemplo, você pode decompor um aplicativo monolítico em um grupo de microsserviços que trabalham em conjunto e que são dimensionados com facilidade.<br/><br/> Ou você pode recriar a arquitetura de seus bancos de dados relacionais e não relacionais em soluções DBaaS totalmente gerenciadas como a Instância Gerenciada do Banco de Dados SQL do Azure, o Banco de Dados do Azure para MySQL, o Banco de Dados do Azure para PostgreSQL e o Azure Cosmos DB. | Quando seus aplicativos precisam de revisões principais para incorporar os novos recursos ou trabalhar com eficiência em uma plataforma de nuvem.<br/><br/> Quando você quiser usar investimentos em aplicativos existentes, atenda aos requisitos de escalabilidade, aplique práticas inovadoras de DevOps do Azure e minimize o uso de máquinas virtuais.
**Recompilar** | A recompilação leva tudo a um novo patamar por meio da recompilação de um aplicativo do zero usando tecnologias de nuvem do Azure.<br/><br/> Por exemplo, você pode criar aplicativos inteiramente novos com tecnologias nativas da nuvem, como sem servidor, AI do Azure, Instância Gerenciada do Banco de Dados SQL do Azure e Azure Cosmos DB. | Quando você quiser um desenvolvimento rápido e os aplicativos existentes têm funcionalidade e vida útil limitadas.<br/><br/> Quando você estiver pronto para agilizar a inovação de negócios (incluindo práticas DevOps fornecidas pelo Azure), crie novos aplicativos usando tecnologias de nuvem nativo e tirar proveito dos avanços em inteligência artificial, blockchain e IoT.

## <a name="migration-articles"></a>Artigos de migração

Os artigos da série são resumidos na tabela a seguir.  

- Cada cenário de migração é orientado por metas de negócios ligeiramente diferentes que determinam a estratégia de migração.
- Para cada cenário de implantação, fornecemos informações sobre fatores e metas comerciais, uma arquitetura proposta, etapas para executar a migração e a recomendação para limpeza e próximas etapas após a conclusão da migração.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
O artigo 1: Visão geral | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Este artigo.
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | Mostra como a Contoso executa uma avaliação de um aplicativo SmartHotel de dois níveis no local em execução no VMware. A Contoso avalia as VMs de aplicativos com o serviço [ do Azure Migrate ](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [ Assistente de Migração de Banco de Dados ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Hospedar novamente um aplicativo em VMs do Azure e uma Instância Gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso executa uma migração lift-and-shift para o Azure para o aplicativo SmartHotel local. A Contoso migra a VM frontend do aplicativo usando [ Azure Site Recovery ](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados do aplicativo para uma Instância Gerenciada do SQL, usando o [ Serviço de Migração do Banco de Dados do Azure ](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra as VMs do aplicativo SmartHotel para VMs do Azure usando o serviço Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo e o serviço Database Migration para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso faz uma migração de elevação e deslocamento do aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e migra o banco de dados do aplicativo para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra sua implantação do TFS (Team Foundation Server) local migrando-a para o VSTS (Visual Studio Team Services) no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível






### <a name="demo-apps"></a>Aplicativos de demonstração

Os artigos usam dois aplicativos de demonstração - SmartHotel e osTicket.

- **SmartHotel360**: esse aplicativo foi desenvolvido pela Microsoft como um aplicativo de teste que você pode usar ao trabalhar com o Azure. Ele é fornecido como o código-fonte aberto e você pode baixá-lo do [GitHub](https://github.com/Microsoft/SmartHotel360). É um aplicativo ASP.NET conectado a um banco de dados do SQL Server. Atualmente, o aplicativo está em duas VMs VMware que executam o Windows Server 2008 R2 e o SQL Server 2008 R2. As VMs do aplicativo estão hospedadas no local e gerenciadas pelo vCenter Server.
- **osTicket**: um aplicativo de geração de tíquetes de suporte técnico de software livre executado no Linux. Você pode baixá-lo do [GitHub](https://github.com/osTicket/osTicket). O aplicativo atual está em duas VMs VMware que executam o Ubuntu 16.04LTS usando o Apache 2, 7.0 PHP e MySQL 5.7
    

## <a name="next-steps"></a>Próximas etapas

[Saiba como](contoso-migration-infrastructure.md) a Contoso configura uma infraestrutura do Azure e local para se preparar para a migração.



