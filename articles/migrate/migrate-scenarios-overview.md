---
title: Migrar seu datacenter local para o Azure | Microsoft Docs
description: Leia um white paper de visão geral sobre como migrar seus data centers locais para o Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167748"
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrando as cargas de trabalho locais para o Azure


O Microsoft Azure fornece acesso a um conjunto abrangente de serviços de nuvem que, como desenvolvedor e profissional de TI, você pode usar para criar, implantar e gerenciar aplicativos em uma variedade de ferramentas e estruturas, por meio de uma rede global de datacenters. Conforme a sua empresa vai enfrentando desafios associados à mudança digital, a nuvem do Azure o ajuda a descobrir como otimizar recursos e operações, envolver-se com seus clientes e funcionários e transformar seus produtos.

No entanto, o Azure reconhece que mesmo com todas as vantagens que a nuvem oferece em termos de velocidade e flexibilidade, redução de custos, desempenho e confiabilidade, muitas organizações ainda precisarão executar datacenters locais por algum tempo. Em resposta aos obstáculos para a adoção da nuvem, o Azure oferece uma estratégia de nuvem híbrida que cria pontes entre seus datacenters locais e a nuvem pública do Azure. Por exemplo, o uso de recursos de nuvem do Azure, como backup para proteger recursos locais, ou o uso de análise do Azure para obter insights sobre cargas de trabalho locais. 

Como parte da estratégia de nuvem híbrida, o Azure fornece soluções de crescimento para migrar aplicativos locais e cargas de trabalho para a nuvem. Com etapas simples, você pode avaliar seus recursos locais de forma abrangente para descobrir como eles serão executados na nuvem do Azure. Com uma avaliação detalhada, você pode migrar seus recursos para o Azure com tranquilidade. Quando os recursos estão em execução no Azure, você pode otimizá-los para manter e melhorar o acesso, a flexibilidade, a segurança e a confiabilidade.

Esta série de artigos de migração mostra como planejar e criar uma estratégia de migração para a sua empresa. Os artigos ilustram um número de cenários que vão crescendo em complexidade e serão adicionados ao longo do tempo. Esses cenários estão resumidos na tabela a seguir. Para cada cenário, fornecemos uma visão geral, uma arquitetura de migração e demonstramos as etapas envolvidas no processo de migração. 

**Cenário** | **Solução** | **Serviços** | **Artigo** 
--- | --- | --- | ---
[Cenário 1: detecção e avaliação](migrate-scenarios-assessment.md) | Descobrir e avaliar aplicativos, dados e infraestruturas locais que devem ser migrados para o Azure | Assistente de Migração de Dados, serviço Migrações para Azure  | Disponível agora
**[Cenário 2: hospedar novamente o aplicativo](migrate-scenarios-lift-and-shift.md)** | Migrar aplicativos para o Azure. | Azure Site Recovery, Serviço de Migração de Banco de Dados do Azure, Instância Gerenciada SQL do Azure | Disponível agora
**Cenário 3: refatorar o aplicativo** | Refatorar aplicativos durante a migração para o Azure. | Em planejamento | Planejado
**Cenário 3: refazer a arquitetura do aplicativo** | Refazer a arquitetura de aplicativos durante a migração para o Azure. | Em planejamento | Planejado
**Cenário 5: recompilar o aplicativo** |Recompilar aplicativos durante a migração para o Azure. | Em planejamento | Planejado




