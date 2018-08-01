---
title: Recomendações de desempenho do Azure Advisor | Microsoft Docs
description: Use o Assistente para otimizar o desempenho das implantações do Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 3331c795cbb1c45820d4c86d287ef57b54f0ae6b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247633"
---
# <a name="advisor-performance-recommendations"></a>Recomendações de desempenho do Advisor

As recomendações de desempenho do Assistente do Azure ajudam a melhorar a velocidade e a capacidade de resposta dos aplicativos críticos para os negócios. Você pode obter recomendações de desempenho do Assistente na guia **Desempenho** do painel do Assistente.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduza o tempo de DNS para viver no seu perfil do Gerenciador de Tráfego para fazer o failover para endpoints saudáveis mais rapidamente

As [configurações de tempo de vida (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) em seu perfil do Gerenciador de Tráfego permitem que você especifique a velocidade de alternar os terminais se um determinado ponto de extremidade parar de responder a consultas. Reduzir os valores de TTL significa que os clientes serão roteados para os endpoints em funcionamento mais rapidamente.

O Azure Advisor identifica os perfis do Gerenciador de Tráfego com um TTL mais longo configurado e recomenda configurar o TTL para 20 segundos ou 60 segundos, dependendo de o perfil estar configurado para [Fast Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho do banco de dados com o Assistente do BD SQL

O Advisor fornece uma exibição consistente e consolidada de recomendações para todos os seus recursos do Azure. Ele se integra ao Advisor do Banco de Dados SQL para fornecer recomendações de melhoria de desempenho para o banco de dados do SQL Azure. O Advisor do Banco de Dados SQL avalia o desempenho dos bancos de dados do SQL Azure analisando o histórico de uso. Em seguida, ele oferece recomendações que são mais adequadas para execução da carga de trabalho típica do banco de dados. 

> [!NOTE]
> Para obter recomendações, um banco de dados deve ter aproximadamente uma semana de uso e dentro dessa semana deve haver atividades consistentes. O Assistente do Banco de Dados SQL pode ser otimizado com mais facilidade para padrões de consulta consistentes do que para intermitências irregulares de atividade.

Para obter mais informações sobre o Assistente do Banco de Dados SQL, consulte [Assistente do Banco de Dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Melhorar o desempenho e a confiabilidade do Cache Redis

O Assistente identifica as instâncias do Cache Redis nas quais o desempenho pode ser prejudicado por alto uso de memória, carga do servidor, largura de banda da rede ou grande número de conexões de cliente. O Assistente também fornece recomendações de melhores práticas para ajudá-lo a evitar possíveis problemas. Para saber mais sobre recomendações do Cache Redis, veja [Advisor do Cache Redis](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e a confiabilidade do Serviço de Aplicativo

O Azure Advisor integra as práticas recomendadas para melhorar sua experiência com os Serviços de Aplicativos e descobrir recursos relevantes de plataforma. Os exemplos de recomendações dos Serviços de Aplicativos são:
* Detecção de instâncias nas quais os recursos de memória ou de CPU são esgotados por tempos de execução de aplicativo com opções de mitigação.
* Detecção de instâncias nas quais a disposição de recursos como aplicativos Web e bancos de dados pode melhorar o desempenho e reduzir custos. 

Para saber mais sobre recomendações de Serviços de Aplicativos, veja [Práticas recomendadas para o Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como acessar as recomendações de desempenho no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  No painel do Assistente, clique na guia **Desempenho**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)

