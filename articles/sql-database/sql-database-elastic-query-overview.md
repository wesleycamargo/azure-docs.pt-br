<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="Consulta de banco de dados elástico – visualização de maio de 2015" 
	description="Anuncia o recurso de consulta elástica" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# Consulta de banco de dados elástico – visualização de junho de 2015 

A consulta de banco de dados elástico é um novo recurso do Bancos de Dados SQL do Azure que será visualizado no final de junho de 2015. Ele introduz a capacidade de executar uma consulta T-SQL que abrange vários bancos de dados usando uma só conexão para um banco de dados SQL do Azure. Isso permite que um conjunto fragmentado de dados, definido usando um mapa de fragmentos de banco de dados, seja exibido como um repositório de dados integrado. Esse recurso é usado para fins de integração de dados e geração de relatórios e todo o processamento de consultas distribuídas é realizado em segundo plano. Conexões são permitidas com qualquer driver capaz de se comunicar com o banco de dados SQL, como ADO.Net, ODBC, JDBC, Node.js, PHP etc.

## Cenários de consulta de banco de dados elástico

O objetivo da consulta de banco de dados elástico é facilitar cenários de relatórios em uma camada de dados fragmentados na qual vários bancos de dados ou fragmentos contribuem linhas para um resultado geral de uma consulta T-SQL. A consulta T-SQL também pode ser composta pelo usuário ou aplicativo diretamente ou indiretamente por meio de ferramentas conectadas ao banco de dados de consultas elástico global. Esse recurso é especialmente útil para BI comercial, relatórios ou ferramentas de integração de dados em pacotes de software que não podem ser estendidos diretamente usando as bibliotecas de dimensionamento elástico. Ele também facilita o acesso a um conjunto inteiro de dados fragmentados por meio de consultas emitidas pelo SQL Server Management Studio ou o Visual Studio e dá suporte ao acesso transparente a dados multifragmentados pelo Entity Framework ou em outros ambientes de ORM. A Figura 1 ilustra esse cenário no qual uma consulta de banco de dados de consultas elástico global oferece um segundo meio para enviar consultas para a camada de dados fragmentados além do aplicativo de nuvem existente (que pode usar as bibliotecas de dimensionamento elástico).

![Consultas de banco de dados elástico][1]

Os cenários podem ser caracterizados pelas seguintes topologias:

-	Camada de dados fragmentados (topologia 1): a camada de dados foi projetada para fragmentação. A fragmentação é executada e gerenciada usando (1) a biblioteca cliente do banco de dados elástico ou (2) autofragmentação. O caso dos relatórios consiste em computar relatórios em vários fragmentos com requisitos funcionais ou de conectividade além das possibilidades da consulta de vários fragmentos na biblioteca cliente do banco de dados elástico. 

-	Design de vários bancos de dados (topologia 2): aqui, a camada de dados é dividida verticalmente, de modo que diferentes tipos de dados residem em bancos de dados diferentes. O caso dos relatórios consiste em calcular relatórios sobre os dados que são distribuídos por vários bancos de dados, mas que devem ser integrados a um resultado geral para o relatório.

A visualização da consulta de banco de dados elástico abrangerá ambos os casos. Os clientes com a topologia 1 podem depender do mapa de fragmentos existente se estiverem usando a biblioteca cliente do banco de dados elástico para gerenciar os fragmentos. Por sua vez, os clientes que adotarem a autofragmentação deverão criar um mapa de fragmentos usando ferramentas de banco de dados elástico para suas camadas de dados para usar esse recurso de visualização. Os clientes com a topologia 2 devem criar um mapa de fragmentos diferente para cada um dos bancos de dados. Cada mapa de fragmentos apontará para apenas um fragmento, que poderá expor suas tabelas para consultas entre fragmentos.

## Próximas etapas
A consulta de banco de dados elástico está programada para visualização no final de junho de 2015. Visite esta página novamente para obter mais detalhes e instruções passo a passo sobre como usar o recurso nessa ocasião.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->