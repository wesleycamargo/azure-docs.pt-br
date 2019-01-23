---
title: Recomendações de desempenho do Azure Advisor | Microsoft Docs
description: Use o Assistente para otimizar o desempenho das implantações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 3caf838fec3a5c0ab847ded85b269df7a66859e0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266738"
---
# <a name="advisor-performance-recommendations"></a>Recomendações de desempenho do Advisor

As recomendações de desempenho do Assistente do Azure ajudam a melhorar a velocidade e a capacidade de resposta dos aplicativos críticos para os negócios. Você pode obter recomendações de desempenho do Assistente na guia **Desempenho** do painel do Assistente.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduza o tempo de DNS para viver no seu perfil do Gerenciador de Tráfego para fazer o failover para endpoints saudáveis mais rapidamente

As [configurações de tempo de vida (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) em seu perfil do Gerenciador de Tráfego permitem que você especifique a velocidade de alternar os terminais se um determinado ponto de extremidade parar de responder a consultas. Reduzir os valores de TTL significa que os clientes serão roteados para os endpoints em funcionamento mais rapidamente.

O Azure Advisor identifica os perfis do Gerenciador de Tráfego com um TTL mais longo configurado e recomenda configurar o TTL para 20 segundos ou 60 segundos, dependendo de o perfil estar configurado para [Fast Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho do banco de dados com o Assistente do BD SQL

O Advisor fornece uma exibição consistente e consolidada de recomendações para todos os seus recursos do Azure. Ele se integra ao Advisor do Banco de Dados SQL para fornecer recomendações de melhoria de desempenho para o banco de dados do SQL Azure. O Advisor do Banco de Dados SQL avalia o desempenho dos bancos de dados do SQL Azure analisando o histórico de uso. Em seguida, ele oferece recomendações que são mais adequadas para execução da carga de trabalho típica do banco de dados.

> [!NOTE]
> Para obter recomendações, um banco de dados deve ter aproximadamente uma semana de uso e dentro dessa semana deve haver atividades consistentes. O Assistente do Banco de Dados SQL pode ser otimizado com mais facilidade para padrões de consulta consistentes do que para intermitências irregulares de atividade.

Para obter mais informações sobre o Assistente do Banco de Dados SQL, consulte [Assistente do Banco de Dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e a confiabilidade do Serviço de Aplicativo

O Azure Advisor integra as práticas recomendadas para melhorar sua experiência com os Serviços de Aplicativos e descobrir recursos relevantes de plataforma. Os exemplos de recomendações dos Serviços de Aplicativos são:
* Detecção de instâncias nas quais os recursos de memória ou de CPU são esgotados por tempos de execução de aplicativo com opções de mitigação.
* Detecção de instâncias nas quais a disposição de recursos como aplicativos Web e bancos de dados pode melhorar o desempenho e reduzir custos.

Para saber mais sobre recomendações de Serviços de Aplicativos, veja [Práticas recomendadas para o Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Usar o Managed Disks para evitar a limitação de E/S do disco

O Assistente identificará as máquinas virtuais que pertencem a uma conta de armazenamento que está atingindo sua meta de escalabilidade. Isso a torna suscetível à limitação de E/S. O Assistente recomendará que essas máquinas virtuais usem Managed Disks para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a confiabilidade dos discos da máquina virtual usando o Armazenamento Premium

O Assistente identifica máquinas virtuais com discos padrão que têm um alto volume de transações em sua conta de armazenamento e recomenda a atualização para discos premium. 

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S. Os discos da máquina virtual que usam contas de Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para o melhor desempenho em seu aplicativo, recomendamos migrar todos os discos de máquinas virtuais que exijam IOPS altos para o armazenamento premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remova a distorção de dados em sua tabela do armazém de dados SQL para aumentar o desempenho da consulta

A distorção de dados pode causar movimentação desnecessária de dados ou gargalos de recursos ao executar sua carga de trabalho. O Advisor detectará uma distorção de dados de distribuição maior que 15% e recomendará que você redistribua seus dados e revisite suas seleções de chave de distribuição de tabela. Para saber mais sobre como identificar e remover distorções, consulte [solução de problemas skew](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Criar ou atualizar as estatísticas de tabela desatualizados em sua tabela do SQL data warehouse para aumentar o desempenho de consulta

O assistente identifica tabelas que não têm atualizadas [estatísticas da tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda a criação ou atualização de estatísticas da tabela. O otimizador de consulta do data warehouse SQL usa estática atualizada para estimar a cardinalidade ou o número de linhas no resultado da consulta, o que permite que o otimizador de consulta crie um plano de consulta de alta qualidade para desempenho mais rápido.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Escalar verticalmente para otimizar a utilização do cache na tabelas do SQL Data Warehouse a fim de aumentar o desempenho de consultas

O Assistente do Azure detecta se o SQL Data Warehouse tem um percentual alto de cache usado e uma baixa porcentagem de acessos. Isso indica que a alta remoção de cache pode afetar o desempenho do seu SQL Data Warehouse. O Assistente sugere escalar verticalmente o SQL Data Warehouse para garantir a alocação de capacidade de cache suficiente para sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas do SQL Data Warehouse em tabelas replicadas para aumentar o desempenho de consultas

O Assistente identifica tabelas que não são replicadas, mas que se beneficiariam da conversão e sugere que você as converta. As recomendações baseiam-se no tamanho da tabela replicada, no número de colunas, no tipo de distribuição da tabela e no número de partições da tabela do SQL Data Warehouse. Outras heurísticas podem ser fornecidas na recomendação para o contexto. Para saber mais sobre como essa recomendação é determinada, consulte [Recomendações do SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migre sua Conta de Armazenamento para o Azure Resource Manager para obter todos os recursos mais recentes do Azure

Migre seu modelo de implantação da Conta de Armazenamento para o ARM (Azure Resource Manager) e usufrua das implantações de modelo, mais opções de segurança e a capacidade de atualizar para uma conta GPv2 para a utilização dos recursos mais recentes do Armazenamento do Microsoft Azure. O Assistente identificará quaisquer contas de armazenamento autônomo que estão usando o modelo de implantação clássico e recomenda a migração para o modelo de implantação do ARM.

> [!NOTE]
> Foi anunciada a desativação dos alertas clássicos do Azure Monitor em junho de 2019. É recomendável que você atualize sua conta de armazenamento clássica para o ARM para manter a funcionalidade de alertas com a nova plataforma. Para saber mais, confira [Desativação dos alertas clássicos](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

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
