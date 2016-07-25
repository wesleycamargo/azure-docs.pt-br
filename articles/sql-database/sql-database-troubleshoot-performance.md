<properties
	pageTitle="Dicas de ajuste de desempenho de Banco de Dados SQL | Microsoft Azure"
	description="Dicas de ajuste de desempenho no Banco de Dados SQL por meio de avaliação e melhoria."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="ajuste de desempenho de sql, ajuste de desempenho de banco de dados, dicas de ajuste de desempenho do sql, ajuste de desempenho de banco de dados sql"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-shysun"/>

# Dicas de ajuste de desempenho do Banco de Dados SQL
Você pode alterar quando quiser a [camada de serviço](sql-database-service-tiers.md) de um banco de dados individual ou aumentar os eDTUs de um pool de banco de dados elástico para melhorar o desempenho, mas convém identificar primeiro as oportunidades de aprimoramento e otimização do desempenho da consulta. Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados. Este artigo fornece orientação para ajuste de desempenho no Banco de Dados SQL.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Etapas para avaliar e ajustar o desempenho do banco de dados
1.	No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, escolha o banco de dados e use o gráfico de monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
2.	Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para avaliar as consultas usando DTUs e use o [Supervisor de Banco de Dados SQL](sql-database-advisor.md) para exibir as recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema.
3.	Você pode usar as DMVs (exibições de gerenciamento dinâmico), os Xevents (Eventos Estendidos) e o Armazenamento de Consulta no SSMS para obter os parâmetros de desempenho em tempo real. Consulte o [tópico com diretrizes sobre desempenho](sql-database-performance-guidance.md) para obter um monitoramento detalhado e dicas de ajuste.


    > [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Etapas para melhorar o desempenho do banco de dados com mais recursos
1.	Para bancos de dados individuais, você pode [alterar as camadas de serviço](sql-database-scale-up.md) sob demanda para melhorar o desempenho do banco de dados.
2.	Para vários bancos de dados, considere o uso de [pools de banco de dados elásticos](sql-database-elastic-pool-guidance.md) para escalar os recursos automaticamente.

<!---HONumber=AcomDC_0713_2016-->