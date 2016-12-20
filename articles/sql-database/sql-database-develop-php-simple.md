---
title: Conectar-se ao Banco de Dados SQL usando PHP no Windows | Microsoft Docs
description: "Apresenta um programa PHP de exemplo que se conecta ao Banco de Dados SQL a partir de um cliente do Windows e fornece links para os componentes de software necessários e exigidos pelo cliente."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 10/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 162c89b07a0bc690600f0cc59e3f4c70e02eac91


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Conectar-se ao Banco de Dados SQL usando PHP no Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico ilustra como você pode se conectar ao Banco de Dados SQL do Azure de um aplicativo cliente escrito em PHP executado no Windows.

## <a name="step-1-configure-development-environment"></a>Etapa 1: configurar o Ambiente de Desenvolvimento
[Configurar um ambiente de desenvolvimento para desenvolvimento PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Etapa 2: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Etapa 3: obter detalhes da conexão
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Etapa 4: Executar o código de exemplo
* [Proof of Concept connecting to SQL using PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Connect resiliently to SQL with PHP](https://msdn.microsoft.com/library/mt720667.aspx)

## <a name="next-steps"></a>Próximas etapas
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft PHP Driver para SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Para saber mais sobre o uso e a instalação de PHP, confira [Acessando os Bancos de Dados SQL com PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


