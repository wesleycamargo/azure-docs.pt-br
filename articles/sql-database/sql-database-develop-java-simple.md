---
title: Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows | Microsoft Docs
description: "Apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo usa o JDBC, e é executado em um computador cliente com Windows."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo de Java se baseia no Java Development Kit (JDK) versão 1.8. O exemplo se conecta a um Banco de Dados SQL do Azure usando o driver JDBC.

## <a name="step-1--configure-development-environment"></a>Etapa 1: configurar o Ambiente de Desenvolvimento
[Configurar um ambiente de desenvolvimento para desenvolvimento Java](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>Etapa 2: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados.  

## <a name="step-3-get-connection-string"></a>Etapa 3: Obter a cadeia de conexão
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> Se você estiver usando o driver JTDS JDBC, você precisará adicionar "ssl=require" à URL da cadeia de conexão e precisará também definir a seguinte opção para o JVM: "-Djsse.enableCBCProtection=false". Essa opção do JVM desabilita uma correção para uma vulnerabilidade de segurança, portanto certifique-se de entender o risco envolvido antes de configurá-la.
> 
> 

## <a name="step-4-run-sample-code"></a>Etapa 4: Executar o código de exemplo
* [Proof of Concept connecting to SQL using Java](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>Próximas etapas
* Visite o [Centro de Desenvolvimento do Java](/develop/java/).
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft JDBC Driver para SQL Server](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)


