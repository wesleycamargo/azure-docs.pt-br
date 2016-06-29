<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando .NET (C#) | Microsoft Azure"
	description="Use o código de exemplo neste início rápido para criar um aplicativo moderno com C# e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="tobiast"/>

# Conectar-se ao Banco de Dados SQL usando .NET (C#)

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## Etapa 1: configurar o Ambiente de Desenvolvimento

[Configurar um ambiente de desenvolvimento para desenvolvimento ADO.NET](https://msdn.microsoft.com/library/mt718321.aspx)

## Etapa 2: Criar um banco de dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## Etapa 3: Obter a cadeia de conexão

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Etapa 4: Executar o código de exemplo

* [Proof of Concept connecting to SQL using ADO.NET](https://msdn.microsoft.com/library/mt718320.aspx) (Prova de conceito, conectar-se ao SQL usando ADO.NET)
* [Connect resiliently to SQL with ADO.NET](https://msdn.microsoft.com/library/mt703195.aspx) (Conectar-se de forma resiliente ao SQL usando ADO.NET)

## Recursos adicionais

Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->