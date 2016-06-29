<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Node.js | Microsoft Azure"
	description="Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="meetb"/>

# Conectar-se ao Banco de Dados SQL usando Node.js

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico mostra como conectar e consultar um Banco de Dados SQL do Azure usando o Node.js. Você pode executar essa amostra de plataformas Windows, Ubuntu Linux ou Mac.

## Etapa 1: configurar o Ambiente de Desenvolvimento

[Prerequisites for using the Tedious Node.js Driver for SQL Server](https://msdn.microsoft.com/library/mt652094.aspx)

## Etapa 2: Criar um banco de dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## Etapa 3: obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 4: Executar o código de exemplo

[Proof of Concept connecting to SQL using Node.js](https://msdn.microsoft.com/library/mt715784.aspx) (Prova de conceito, conectar-se ao SQL usando Node.js)

## Recursos adicionais

Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->