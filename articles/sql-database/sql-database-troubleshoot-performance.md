<properties
	pageTitle="Como avaliar e melhorar o desempenho do banco de dados no Banco de Dados SQL do Azure"
	description="Descreve como avaliar e melhorar o desempenho do banco de dados"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="v-shysun"/>

# Como avaliar e melhorar o desempenho do banco de dados no Banco de Dados SQL do Azure
Você pode alterar quando quiser a [camada de serviço](sql-database-service-tiers.md) de um banco de dados individual ou aumentar os eDTUs de um pool de banco de dados elástico para melhorar o desempenho, mas convém identificar primeiro as oportunidades de aprimoramento e otimização do desempenho da consulta. Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados.

## Etapas para avaliar e ajustar o desempenho do banco de dados
1.	No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, escolha o banco de dados e use o gráfico de monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
2.	Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para avaliar as consultas usando DTUs e, em seguida, use o [Advisor do Banco de Dados SQL](sql-database-index-advisor.md) para exibir as recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema.
3.	Você pode usar as DMVs (exibições de gerenciamento dinâmico), os Xevents (Eventos Estendidos) e o Armazenamento de Consulta no SSMS para obter os parâmetros de desempenho em tempo real. Consulte o [tópico com diretrizes sobre desempenho](sql-database-performance-guidance.md) para obter um monitoramento detalhado e dicas de ajuste.

## Etapas para melhorar o desempenho do banco de dados com mais recursos
1.	Para bancos de dados individuais, você pode [alterar as camadas de serviço](sql-database-scale-up.md) sob demanda para melhorar o desempenho do banco de dados.
2.	Para vários bancos de dados, considere o uso de [pools de banco de dados elásticos](sql-database-elastic-pool-guidance.md) para escalar os recursos automaticamente.

Se o problema de desempenho continuar, contate o suporte e abra um caso de suporte.

<!---HONumber=AcomDC_0511_2016-->