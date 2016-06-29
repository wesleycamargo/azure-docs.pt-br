<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Ruby | Microsoft Azure"
	description="Dê um exemplo de código Ruby que você pode executar para se conectar ao Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="andrela"/>


# Conectar-se ao Banco de Dados SQL usando o Ruby 

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico mostra como conectar e consultar um Banco de Dados SQL do Azure usando o Ruby. Você pode executar essa amostra de plataformas Windows, Ubuntu Linux ou Mac.

## Etapa 1: configurar o Ambiente de Desenvolvimento

[Prerequisites for using the TinyTDS Ruby Driver for SQL Server](https://msdn.microsoft.com/library/mt711041.aspx)

## Etapa 2: Criar um banco de dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## Etapa 3: obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 4: Executar o código de exemplo

[Proof of Concept connecting to SQL using Ruby](http://msdn.microsoft.com/library/mt715797.aspx) (Prova de conceito, conectar-se ao SQL usando Ruby)

## Recursos adicionais

Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->