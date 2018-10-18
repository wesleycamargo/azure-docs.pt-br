---
title: Recomendações de desempenho do Azure Advisor | Microsoft Docs
description: Use o Assistente para otimizar o desempenho das implantações do Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: 9516534216c4a2c0f61e33ea3cbf1bbcb2ab58c7
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301303"
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

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remova a distorção de dados em sua tabela do armazém de dados SQL para aumentar o desempenho da consulta

A distorção de dados pode causar movimentação desnecessária de dados ou gargalos de recursos ao executar sua carga de trabalho. O Advisor detectará uma distorção de dados de distribuição maior que 15% e recomendará que você redistribua seus dados e revisite suas seleções de chave de distribuição de tabela. Para saber mais sobre como identificar e remover distorções, consulte [solução de problemas skew](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Criar ou atualizar as estatísticas de tabela desatualizados em sua tabela do SQL data warehouse para aumentar o desempenho de consulta

O assistente identifica tabelas que não têm atualizadas [estatísticas da tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda a criação ou atualização de estatísticas da tabela. O otimizador de consulta do data warehouse SQL usa estática atualizada para estimar a cardinalidade ou o número de linhas no resultado da consulta, o que permite que o otimizador de consulta crie um plano de consulta de alta qualidade para desempenho mais rápido.

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migre sua Conta de Armazenamento para o Azure Resource Manager para obter todos os recursos mais recentes do Azure

Migre seu modelo de implantação da Conta de Armazenamento para o ARM (Azure Resource Manager) e usufrua das implantações de modelo, mais opções de segurança e a capacidade de atualizar para uma conta GPv2 para a utilização dos recursos mais recentes do Armazenamento do Microsoft Azure. O Assistente identificará quaisquer contas de armazenamento autônomo que estão usando o modelo de implantação clássico e recomenda a migração para o modelo de implantação do ARM. 

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

