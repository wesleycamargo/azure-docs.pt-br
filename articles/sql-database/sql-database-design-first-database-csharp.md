---
title: Criar seu primeiro banco de dados SQL do Azure - C# | Microsoft Docs
description: Aprenda a criar seu primeiro banco de dados SQL do Azure e conectá-lo a um programa em C# usando o ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727158"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Tutorial: Criar um banco de dados SQL do Azure e conectar-se com C&#x23 e o ADO.NET

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (Azure). Neste tutorial, você aprenderá a usar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
> * Criar um banco de dados no Portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conecte-se ao banco de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e excluir dados com o ADO.NET
> * Consultar ADO.NET de dados

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu tarefas básicas de banco de dados, como criar um banco de dados e tabelas, conectar-se ao banco de dados, carregar os dados e executar consultas. Você aprendeu como:

> [!div class="checklist"]
> * Criar um banco de dados
> * Configurar uma regra de firewall
> * Conectar-se ao banco de dados com o [Visual Studio e C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar, excluir e consultar dados

Avança para o próximo tutorial para saber mais sobre migração de dados.

> [!div class="nextstepaction"]
> [Migrar SQL Server para Banco de Dados SQL do Azure offline usando DMS](../dms/tutorial-sql-server-to-azure-sql.md)
