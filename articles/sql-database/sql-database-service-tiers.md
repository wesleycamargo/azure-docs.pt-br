<properties
	pageTitle="Opções e desempenho do Banco de Dados SQL: camadas de serviço | Microsoft Azure"
	description="Compare os recursos de continuidade dos negócios e o desempenho do Banco de Dados SQL das camadas de serviço para equilibrar o custo e o recurso à medida que você dimensiona."
	keywords="opções de banco de dados, desempenho do banco de dados"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor="CarlRabeler"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/10/2016"
	ms.author="carlrab"/>

# Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço

O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) tem várias camadas de serviço para lidar com cargas de trabalho diferentes. Você pode [alterar os níveis de serviço](sql-database-scale-up.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Você também pode [criar um banco de dados individual](sql-database-get-started.md) com características e preços definidos. Ou você pode gerenciar vários bancos de dados [criando um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md). Em ambos os casos, as camadas incluem **Básica**, **Standard** e **Premium**. As opções de banco de dados nesses níveis são semelhantes bancos de dados autônomos e pools elásticos, mas há considerações adicionais sobre os pools elásticos. Este artigo fornece detalhes sobre camadas de serviço para bancos de dados autônomos e pools elásticos.

## Opções de camadas de serviço e banco de dados
Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99% e oferecem desempenho previsível, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
|---|---|
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** | A opção ideal para a maioria dos aplicativos em nuvem, permitindo várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para alto volume transacional, permitindo vários usuários simultâneos e exigindo o mais alto nível de recursos de continuidade de negócios. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |

>[AZURE.NOTE] As edições Web e Business foram descontinuadas. Leia [Perguntas frequentes sobre a descontinuação](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se planeja continuar usando as edições Web e Business.

## Camadas de serviço e níveis de desempenho de banco de dados autônomo
Para bancos de dados autônomos, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às suas demandas de carga de trabalho. Se você precisar expandir ou reduzir, poderá alterar facilmente as camadas do banco de dados. Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](sql-database-scale-up.md) para obter detalhes.

As características de desempenho listadas aqui se aplicam a bancos de dados criados com o [Banco de Dados SQL V12](sql-database-v12-whats-new.md). Independentemente do número de bancos de dados hospedados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[AZURE.INCLUDE [Tabela de camadas de serviço do Banco de Dados SQL](../../includes/sql-database-service-tiers-table.md)]

Para entender melhor as DTUs, veja a [seção DTUs](#understanding-dtus) neste tópico.

>[AZURE.NOTE] Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Camadas de serviço e desempenho em eDTUs do pool elástico
Além de criar e escalar um banco de dados autônomo, você também tem a opção de gerenciar vários bancos de dados dentro de um [pool elástico](sql-database-elastic-pool.md). Todos os bancos de dados em um pool elástico compartilham um conjunto comum de recursos. As características de desempenho são medidas pelas *Unidades de Transação de Banco de Dados Elástico* (eDTUs). Da mesma forma que ocorre com os bancos de dados autônomos, os pools são fornecidos em três camadas de desempenho: **Básico**, **Standard** e **Premium**. Para os pools, essas três camadas de serviço ainda definem os limites de desempenho geral e vários outros recursos.

Pools permitem que esses bancos de dados compartilhem e consumam os recursos de DTU sem a necessidade de atribuir um nível de desempenho específicos para todos os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de eDTU de banco de dados configurado por você durante a definição do pool. Os pools permitem que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente. Confira [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela a seguir descreve as características das camadas de serviço do pool.

[AZURE.INCLUDE [Tabela de níveis de serviço de BD SQL para pools elásticos](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características do banco de dados autônomo para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 4800 a 28800, mas um banco de dados individual dentro de um pool Básico tem um limite de banco de dado de 300 sessões.

## Noções básicas sobre DTUs

[AZURE.INCLUDE [Descrição de DTU de Banco de Dados SQL](../../includes/sql-database-understanding-dtus.md)]

## Como escolher uma camada de serviço

Para decidir sobre uma camada de serviço, comece determinando se o banco de dados deverá ser um banco de dados autônomo ou se fará parte de um pool elástico.

### Como escolher uma camada de serviço para um banco de dados autônomo

Para decidir sobre uma camada de serviço para um banco de dados autônomo, comece determinando os recursos do banco de dados necessários para escolher sua edição do Banco de Dados SQL:

- Tamanho do banco de dados (no máximo 5 GB para o Básico, 250 GB para o Standard e 500 GB a 1 TB para Premium, dependendo do nível de desempenho)
- Período de retenção de backup do banco de dados (sete dias para Basic, 35 dias para Standard e 35 dias para Premium)

Depois de determinar a edição do Banco de Dados SQL, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Você pode fazer uma suposição e depois [Escalar ou reduzir verticalmente ou horizontalmente](sql-database-scale-up.md) com base na experiência real. Você também pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários.

### Como escolher uma camada de serviço para um pool de banco de dados elástico.

Para decidir sobre uma camada de serviço para um pool de banco de dados elástico, comece determinando os recursos do banco de dados necessários para escolher a camada de serviço para seu pool.

- Tamanho do banco de dados (2 GB para Básico, 250 GB para Standard e 500 GB para Premium)
- Período de retenção de backup do banco de dados (sete dias para Basic, 35 dias para Standard e 35 dias para Premium)
- Número de bancos de dados por pool (400 para Básico, 400 para Standard e 50 para Premium)
- Armazenamento máximo por pool (117 GB para Básico, 1200 para Standard e 750 para Premium)

Depois de determinar a camada de serviço para o pool, você estará pronto para determinar o nível de desempenho do pool (eDTUs). Você pode fazer uma suposição e depois [Escalar ou reduzir verticalmente ou horizontalmentede modo dinâmico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) com base na experiência real. Você pode usar a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários para cada banco de dados em um pool, a fim de ajudar a definir o limite superior para o pool.

## Próximas etapas
- Saiba mais sobre os preços para essas camadas em [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Conheça os detalhes dos [pools elásticos](sql-database-elastic-pool-guidance.md) e [considerações sobre o preço e o desempenho dos pools elásticos](sql-database-elastic-pool-guidance.md).
- Saiba como [Monitorar, gerenciar e redimensionar pools elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorar o desempenho de bancos de dados autônomos](sql-database-single-database-monitor.md).
- Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md).

## Recursos adicionais

Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0817_2016-->