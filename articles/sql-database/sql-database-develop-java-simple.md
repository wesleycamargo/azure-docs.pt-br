---
title: Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows | Microsoft Docs
description: Apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo usa o JDBC, e é executado em um computador cliente com Windows.
services: sql-database
documentationcenter: ''
author: LuisBosquez
manager: jhubbard
editor: genemi

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/16/2016
ms.author: lbosq

---
# Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows
[!INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo de Java se baseia no Java Development Kit (JDK) versão 1.8. O exemplo se conecta a um Banco de Dados SQL do Azure usando o driver JDBC.

## Etapa 1: configurar o Ambiente de Desenvolvimento
[Configurar um ambiente de desenvolvimento para desenvolvimento Java](https://msdn.microsoft.com/library/mt720658.aspx)

## Etapa 2: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados.

## Etapa 3: Obter a cadeia de conexão
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> Se você estiver usando o driver JTDS JDBC, você precisará adicionar "ssl=require" à URL da cadeia de conexão e precisará também definir a seguinte opção para o JVM: "-Djsse.enableCBCProtection=false". Essa opção do JVM desabilita uma correção para uma vulnerabilidade de segurança, portanto certifique-se de entender o risco envolvido antes de configurá-la.
> 
> 

## Etapa 4: Executar o código de exemplo
* [Proof of Concept connecting to SQL using Java](https://msdn.microsoft.com/library/mt720656.aspx) (Prova de conceito, conectar-se ao SQL usando Java)

## Próximas etapas
* Visite o [Centro de Desenvolvimento do Java](/develop/java/).
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft JDBC Driver para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->