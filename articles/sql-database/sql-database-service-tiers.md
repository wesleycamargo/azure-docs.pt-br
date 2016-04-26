<properties
	pageTitle="Opções e desempenho do Banco de Dados SQL: camadas de serviço | Microsoft Azure"
	description="Compare os recursos de continuidade dos negócios e o desempenho do Banco de Dados SQL das camadas de serviço para equilibrar o custo e o recurso à medida que você dimensiona."
	keywords="opções de banco de dados, desempenho do banco de dados"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/19/2016"
	ms.author="carlrab"/>

# Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço

O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) tem várias camadas de serviço para lidar com cargas de trabalho diferentes. Você pode alterar as camadas de serviço a qualquer momento sem tempo de inatividade para seu aplicativo. Você também pode [criar um banco de dados individual](sql-database-get-started.md) com características e preços definidos. Ou você pode gerenciar vários bancos de dados [criando um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md). Em ambos os casos, as camadas incluem **Básica**, **Standard** e **Premium**. As opções de banco de dados nesses níveis são semelhantes bancos de dados individuais e pools elásticos, mas há considerações adicionais sobre os pools elásticos. Este artigo fornece detalhes sobre camadas de serviço para bancos de dados individuais e elásticos.

## Opções de camadas de serviço e banco de dados
Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99% e oferecem desempenho previsível, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
|---|---|
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** | A opção ideal para a maioria dos aplicativos em nuvem, permitindo várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para alto volume transacional, permitindo um grande número de usuários simultâneos e exigindo o mais alto nível de recursos de continuidade de negócios. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |

>[AZURE.NOTE] As edições Web e Business foram descontinuadas. Leia [Perguntas frequentes sobre a descontinuação](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se planeja continuar usando as edições Web e Business.

## Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às suas demandas de carga de trabalho. Se precisar escalar verticalmente ou horizontalmente, você poderá alterar as camadas do banco de dados com facilidade, **sem tempo de inatividade dos aplicativos.** Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](sql-database-scale-up.md) para obter detalhes.

As características de desempenho listadas aqui se aplicam a bancos de dados criados com o [Banco de Dados SQL V12](sql-database-v12-whats-new.md). Em situações em que o hardware subjacente no Azure hospeda vários bancos de dados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[AZURE.INCLUDE [Tabela de camadas de serviço do Banco de Dados SQL](../../includes/sql-database-service-tiers-table.md)]

Para entender melhor as DTUs, consulte o [seção sobre DTUs](#understanding-dtus) neste tópico.

>[AZURE.NOTE] Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Camadas de serviço e desempenho em eDTUs do pool elástico
Além de criar e escalar um banco de dados individual, você também tem a opção de gerenciar vários bancos de dados dentro de um [pool elástico](sql-database-elastic-pool.md). Todos os bancos de dados em um pool elástico compartilham um conjunto comum de recursos. As características de desempenho são medidas pelas *Unidades de Transação de Banco de Dados Elástico* (eDTUs). Da mesma forma que ocorre com os bancos de dados individuais, os pools são fornecidos em três camadas de desempenho: **Básico**, **Standard** e **Premium**. Para os pools, essas três camadas de serviço ainda definem os limites de desempenho geral e vários outros recursos.

Pools permitem que esses bancos de dados elásticos compartilhem e consumam os recursos de DTU sem a necessidade de atribuir um nível de desempenho específicos para os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de eDTU de banco de dados configurado por você durante a definição do pool. Isso permite que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente. Confira [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md) para obter detalhes.

A tabela a seguir descreve as características das camadas de serviço do pool.

[AZURE.INCLUDE [Tabela de níveis de serviço de BD SQL para bancos de dados elásticos](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características do banco de dados individual para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 2400 a 28800, mas um banco de dados individual dentro desse pool tem um limite de banco de dados de 300 sessões (o limite para um banco de dados Basic individual conforme especificado na seção anterior).

## Noções básicas sobre DTUs

[AZURE.INCLUDE [Descrição de DTU de Banco de Dados SQL](../../includes/sql-database-understanding-dtus.md)]

## Próximas etapas
- Saiba mais sobre os preços para essas camadas em [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Conheça os detalhes dos [pools de banco de dados elásticos](sql-database-elastic-pool-guidance.md) e [considerações sobre o preço e o desempenho dos pools de banco de dados elásticos](sql-database-elastic-pool-guidance.md).
- Saiba como [Monitorar, gerenciar e redimensionar pools elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorar o desempenho de bancos de dados individuais](sql-database-single-database-monitor.md).
- Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md)!

<!---HONumber=AcomDC_0420_2016-->