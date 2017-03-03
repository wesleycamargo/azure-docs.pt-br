---
title: Conectar-se ao Banco de Dados SQL usando .NET (C#) | Microsoft Docs
description: "Use o código de exemplo neste início rápido para criar um aplicativo moderno com C# e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: tobbox
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/03/2017
ms.author: tobiast
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cc756c662b97c64ab30a7d5bf2cc325f9e74d905
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-net-c"></a>Conectar-se ao Banco de Dados SQL usando .NET (C#)
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

## <a name="step-1--configure-development-environment"></a>Etapa 1: configurar o Ambiente de Desenvolvimento
[Configurar um ambiente de desenvolvimento para desenvolvimento ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-1-configure-development-environment-for-ado-net-development/)

## <a name="step-2-create-a-sql-database"></a>Etapa 2: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.  

## <a name="step-3--get-connection-string"></a>Etapa 3: obter a cadeia de conexão
[!INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Etapa 4: Executar o código de exemplo
* [Proof of Concept connecting to SQL using ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-3-proof-of-concept-connecting-to-sql-using-ado-net/)
* [Connect resiliently to SQL with ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net/)

## <a name="next-steps"></a>Próximas etapas
* [Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft ADO.NET Driver para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)


