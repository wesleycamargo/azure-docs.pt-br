---
title: Melhorar o desempenho de aplicativos do Azure com o Assistente do Azure | Microsoft Docs
description: Use o Assistente para otimizar o desempenho das implantações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 6cca6692da37714c76f5241ed14e24c967b00563
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467690"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho de aplicativos do Azure com o Assistente do Azure

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

O Assistente identificará as máquinas virtuais que pertencem a uma conta de armazenamento que está atingindo sua meta de escalabilidade. Essa condição torna essas VMs suscetíveis à limitação de E/S. O Assistente recomendará que elas usem o Managed Disks para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a confiabilidade dos discos da máquina virtual usando o Armazenamento Premium

O Assistente identifica máquinas virtuais com discos padrão que têm um alto volume de transações em sua conta de armazenamento e recomenda a atualização para discos premium. 

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S. Os discos da máquina virtual que usam contas de Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para o melhor desempenho em seu aplicativo, recomendamos migrar todos os discos de máquinas virtuais que exijam IOPS altos para o armazenamento premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remova a distorção de dados em sua tabela do armazém de dados SQL para aumentar o desempenho da consulta

A distorção de dados pode causar movimentação desnecessária de dados ou gargalos de recursos ao executar sua carga de trabalho. O Advisor detectará uma distorção de dados de distribuição maior que 15% e recomendará que você redistribua seus dados e revisite suas seleções de chave de distribuição de tabela. Para saber mais sobre como identificar e remover distorções, consulte [solução de problemas skew](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Criar ou atualizar as estatísticas de tabela desatualizados em sua tabela do SQL data warehouse para aumentar o desempenho de consulta

O assistente identifica tabelas que não têm atualizadas [estatísticas da tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda a criação ou atualização de estatísticas da tabela. O otimizador de consulta do data warehouse SQL usa estática atualizada para estimar a cardinalidade ou o número de linhas no resultado da consulta, o que permite que o otimizador de consulta crie um plano de consulta de alta qualidade para desempenho mais rápido.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Escalar verticalmente para otimizar a utilização do cache na tabelas do SQL Data Warehouse a fim de aumentar o desempenho de consultas

O Assistente do Azure detecta se o SQL Data Warehouse tem um percentual alto de cache usado e uma baixa porcentagem de acessos. Essa condição indica alta remoção de cache o que pode afetar o desempenho do SQL Data Warehouse. O Assistente sugere escalar verticalmente o SQL Data Warehouse para garantir a alocação de capacidade de cache suficiente para sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas do SQL Data Warehouse em tabelas replicadas para aumentar o desempenho de consultas

O Assistente identifica tabelas que não são replicadas, mas que se beneficiariam da conversão e sugere que você as converta. As recomendações baseiam-se no tamanho da tabela replicada, no número de colunas, no tipo de distribuição da tabela e no número de partições da tabela do SQL Data Warehouse. Outras heurísticas podem ser fornecidas na recomendação para o contexto. Para saber mais sobre como essa recomendação é determinada, consulte [Recomendações do SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migre sua Conta de Armazenamento para o Azure Resource Manager para obter todos os recursos mais recentes do Azure

Migre seu modelo de implantação da Conta de Armazenamento para o Azure Resource Manager (Resource Manager) e usufrua das implantações de modelo, de mais opções de segurança e da capacidade de atualizar para uma conta GPv2 a fim de poder usar os recursos mais recentes do Armazenamento do Azure. O Assistente identificará quaisquer contas de armazenamento autônomo que estão usando o modelo de implantação clássico e recomenda a migração para o modelo de implantação do Resource Manager.

> [!NOTE]
> Os alertas clássicos no Azure Monitor estão programados para desativação em junho de 2019. Recomendamos que você atualize sua conta de armazenamento clássica para usar o Resource Manager e manter a funcionalidade de alerta com a nova plataforma. Para saber mais, confira [Desativação dos alertas clássicos](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Projetar suas contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure pode dar suporte a um máximo de 250 contas de armazenamento por assinatura. Depois que o limite for atingido, não será possível criar mais contas de armazenamento nessa combinação de região/assinatura. O Supervisor verificará suas assinaturas e recomendações de superfície para a criação de contas de armazenamento menos para os que estão perto de atingir o limite máximo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Otimizar o desempenho dos seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corrigir a pressão da CPU dos seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB com afunilamentos de CPU
Muito alta utilização da CPU por um longo período pode causar baixo desempenho de consulta para sua carga de trabalho. Aumentar o tamanho de CPU, ajudar a otimizar o tempo de execução das consultas de banco de dados e melhorar o desempenho geral. O Azure Advisor identificar os servidores com uma alta utilização da CPU que provavelmente estão executando cargas de trabalho de CPU restrita e recomendável dimensionar sua computação.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduza as restrições de memória em seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB ou mudança para uma memória otimizada SKU
Uma taxa de acertos do cache de baixa pode resultar em desempenho de consulta mais lento e maior IOPS. Isso pode ser devido a um plano de consulta incorretos ou executando uma carga de trabalho com uso intensivo de memória. Corrigindo o plano de consulta ou [aumentando a memória](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) do banco de dados do Azure para servidor de banco de dados PostgreSQL, servidor de banco de dados MySQL do Azure ou Azure MariaDB server ajudará a otimizar a execução da carga de trabalho de banco de dados. O Azure Advisor identifica os servidores afetados devido a essa variação do pool de buffer de alta e recomenda um plano de consulta, corrigindo movendo para um SKU superior com mais memória ou aumentando o tamanho de armazenamento para obter mais IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Use um MySQL do Azure ou a réplica de leitura do PostgreSQL do Azure para escalar horizontalmente leituras para cargas de trabalho com uso intensivo de leitura
O Assistente do Azure aproveita a heurística com base em carga de trabalho, como a taxa de leituras para gravações no servidor nos últimos sete dias para identificar as cargas de trabalho de leitura intensa. Seu banco de dados do Azure para PostgreSQL recursos ou o banco de dados do Azure para MySQL de recurso com uma taxa muito alta de leitura/gravação pode resultar em contenções de CPU e/ou memória levando a reduzir o desempenho da consulta. Adicionando um [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ajudará na expansão de leituras para o servidor de réplica, impedindo que as restrições de memória e/ou da CPU no servidor primário. O Supervisor identificará servidores com tais altas cargas de trabalho de leitura intensa e recomendável adicionar um [ler réplica](https://docs.microsoft.com/en-us/azure/postgresql/concepts-read-replicas) para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Escalar seu servidor do Azure MySQL, PostgreSQL do Azure ou Azure MariaDB para um SKU superior para impedir que as restrições de conexão
Cada nova conexão ao seu servidor de banco de dados ocupa alguma memória. Se as conexões ao seu servidor estão falhando por causa da degrada o desempenho do servidor de banco de dados uma [limite superior](https://docs.microsoft.com/en-us/azure/postgresql/concepts-limits) na memória. O Assistente do Azure será identificar servidores que executam com muitas falhas de conexão e recomendável fazer a atualização de limites de conexões do seu servidor para fornecer mais memória para seu servidor ao escalar verticalmente a computação ou usar os SKUs de otimizado da memória, que tem mais computação por núcleo.

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
