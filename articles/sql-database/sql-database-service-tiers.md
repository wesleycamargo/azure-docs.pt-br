<properties
   pageTitle="Camadas de serviço do Banco de Dados SQL"
   description="Compare os recursos de continuidade dos negócios e desempenho das camadas de serviço do Banco de Dados SQL do Azure para encontrar o equilíbrio certo entre custo e capacidade à medida que as escala horizontalmente sob demanda e sem tempo de inatividade."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/13/2015"
   ms.author="shkurhek"/>

# Camadas de serviço do Banco de Dados SQL

## Visão geral
O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) fornece várias camadas de serviço para lidar com diferentes tipos de cargas de trabalho. Você tem a opção de [criar um banco de dados individual](sql-database-get-started.md) com características e preços definidos. Ou você pode gerenciar vários bancos de dados [criando um pool de banco de dados elástico](sql-database-elastic-pool-portal.md). Em ambos os casos, as camadas incluem **Básica**, **Standard** e **Premium**. Mas as características dessas camadas variam conforme você cria um banco de dados individual ou um pool de banco de dados elástico. Este artigo fornece uma visão geral das camadas de serviço em ambos os contextos.

## Camadas de serviço
Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99% e oferecem desempenho previsível, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
|---|---|
| **Básico** | Mais adequada para um banco de dados de tamanho pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** | A opção ideal para a maioria dos aplicativos em nuvem, permitindo várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para alto volume transacional, permitindo um grande número de usuários simultâneos e exigindo o mais alto nível de recursos de continuidade de negócios. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |

>[AZURE.NOTE]As edições Web e Business estão sendo desativadas. Descubra como [atualizar as edições Web e Business](sql-database-upgrade-new-service-tiers.md). Leia [Perguntas frequentes sobre a descontinuação](http://azure.microsoft.com/pricing/details/sql-database/web-business/) se planeja continuar usando as edições Web e Business.

### Camadas de serviço para bancos de dados individuais
Para bancos de dados individuais existem vários níveis de desempenho em cada camada de serviço, você tem a flexibilidade de escolher o nível que melhor atende às suas demandas de carga de trabalho. Se precisar expandir ou reduzir, você poderá alterar as camadas do banco de dados com facilidade no Portal do Azure, sem tempo de inatividade dos aplicativos. Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](sql-database-scale-up.md) para obter detalhes.

As características de desempenho listadas aqui se aplicam a bancos de dados criados com o [Banco de Dados SQL V12](sql-database-v12-whats-new.md).

[AZURE.INCLUDE [Tabela de camadas de serviço do Banco de Dados SQL](../../includes/sql-database-service-tiers-table.md)]

### Camadas de serviço para pools de banco de dados elástico
Além de criar e escalar um banco de dados individual, você também tem a opção de gerenciar vários bancos de dados dentro de um [pool de banco de dados elástico](sql-database-elastic-pool.md). Todos os bancos de dados em um pool de banco de dados elástico compartilham um conjunto comum de recursos. As características de desempenho são medidas pelas *Unidades de Transação de Banco de Dados Elástico* (eDTUs). Da mesma forma que ocorre com os bancos de dados individuais, os pools de banco de dados elástico são fornecidos em três camadas de desempenho: **Básico**, **Standard** e **Premium**. Para bancos de dados elásticos essas três camadas de serviço ainda definem os limites de desempenho geral e vários outros recursos.

Pools de banco de dados elástico permitem que esses bancos de dados compartilhem e consumam os recursos DTU sem a necessidade de atribuir um nível de desempenho específicos para os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de banco de eDTU de banco de dados (qualquer um dos 100 eDTUs definidos pela camada de serviço ou um número personalizado configurado por você). Isso permite que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente.

A tabela a seguir descreve as características das camadas de serviço do pool de banco de dados elástico.

[AZURE.INCLUDE [Tabela de níveis de serviço de BD SQL para bancos de dados elásticos](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

>[AZURE.NOTE]Cada banco de dados dentro de um pool também cumpre as características do banco de dados individual para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 2400 a 28800, mas um banco de dados individual dentro desse pool tem um limite de banco de dados de 300 sessões (o limite para um banco de dados Basic individual conforme especificado na seção anterior).

## Noções básicas sobre DTUs

[AZURE.INCLUDE [Descrição de DTU de Banco de Dados SQL](../../includes/sql-database-understanding-dtus.md)]

## Monitoramento de desempenho
O monitoramento do desempenho de um Banco de Dados SQL começa com o monitoramento da utilização de recursos em relação ao nível de desempenho escolhido para seu banco de dados. Esses dados relevantes são expostos das seguintes maneiras:

1.	No Portal de Gerenciamento do Microsoft Azure.

2.	Nas Exibições de Gerenciamento Dinâmico no banco de dados do usuário e no banco de dados mestre do servidor que contém o banco de dados do usuário.

No [portal de Visualização do Azure](https://portal.azure.com/), é possível monitorar a utilização de um banco de dados individual selecionando-o e clicando no gráfico de **Monitoramento**. Isso abre uma janela **Métrica** que pode ser alterada clicando no botão **Editar gráfico**. Adicione as seguintes métricas:

- Porcentagem de CPU
- Porcentagem de DTU
- Porcentagem de E/S de dados
- Porcentagem de armazenamento

Depois de adicionar essas métricas, você pode continuar a exibi-las no gráfico **Monitoramento** com mais detalhes na janela **Métrica**. Todas as quatro métricas mostram o percentual médio de utilização relativo à **DTU** do seu banco de dados.

![monitoramento da camada de serviço](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Você também pode configurar alertas nas métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métrica**. Siga o Assistente para configurar o alerta. Você tem a opção de alerta se a métrica exceder um limite determinado ou se ficar abaixo de um limite determinado.

Por exemplo, se você espera que a carga de trabalho em seu banco de dados cresça, você poderá configurar um alerta por email sempre que seu banco de dados atinge 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para decidir quando precisará alternar para o próximo nível mais alto de desempenho.

As métricas de desempenho podem ajudá-lo a determinar se você pode fazer downgrade para um nível de desempenho inferior. Suponha que você está usando um banco de dados Standard S2 e todas as métricas de desempenho mostram que o banco de dados em média não usa mais de 10% a qualquer momento. É provável que o banco de dados funcione bem em Standard S1. No entanto, tome cuidado com cargas de trabalho que apresentam picos ou oscilam antes de tomar a decisão de migrar para um nível de desempenho inferior.

As mesmas métricas que são expostas no portal também estão disponíveis por meio de exibições do sistema: [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) no banco de dados mestre lógico do seu servidor e [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) no banco de dados do usuário (**sys.dm\_db\_resource\_stats** é criado em cada banco de dados de usuário Básico, Standard e Premium. Bancos de dados das edições Web e Business retornam um conjunto de resultados vazio). Use **sys.resource\_stats** se precisar monitorar dados menos granulares em um período de tempo mais longo. Use **sys.dm\_db\_resource\_stats** se precisar monitorar dados mais granulares em um período de tempo mais curto. Para obter mais informações, veja [Orientação sobre o desempenho do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn369873.aspx).

Para pools de banco de dados elástico, você pode monitorar bancos de dados individuais no pool com as técnicas descritas nesta seção. Mas você também pode monitorar o pool como um todo. Para obter informações, veja [Monitorar e gerenciar um pool de banco de dados elástico](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool).

## Próximas etapas
Saiba mais sobre os preços para essas camadas em [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

Se estiver interessado em gerenciar vários bancos de dados como um grupo, considere usar [pools de banco de dados elástico](sql-database-elastic-pool-guidance.md), juntamente com as [considerações sobre preço e desempenho para pools de banco de dados elástico](sql-database-elastic-pool-guidance.md) associadas.

Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md)!
 

<!---HONumber=Oct15_HO3-->