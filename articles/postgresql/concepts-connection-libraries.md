---
title: "Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: Lista cada biblioteca ou driver que os programas de cliente podem usar ao se conectarem ao Banco de Dados do Azure para PostgreSQL.
keywords: azure cloud postgresql postgres
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL
Este tópico lista cada biblioteca ou driver que os programas de cliente podem usar ao se conectarem ao Banco de Dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem para se conectar ao servidor PostgreSQL são projetos externos distribuídos de forma independente. Elas têm suporte nas plataformas Windows, Linux e Mac. Alguns dos drivers de cliente populares estão listados abaixo.
| **Linguagem** | **Interface do cliente** | **Informações adicionais** | **Baixar** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Em confomridade com a DB API 2.0 | [Baixar](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Extensão de banco de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Cliente puro do JavaScript sem bloqueio | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Driver JDBC do tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Baixar](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver de postgres Go puro | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Provedor de dados ADO.NET | [Baixar](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Baixar](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface primária de linguagem C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interface de C++ com novo estilo | [Baixar](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para PostgreSQL](overview.md).
- Para obter mais detalhes sobre servidores, confira [Banco de Dados do Azure para servidores PostgreSQL](concepts-servers.md).
- Para criar seu primeiro servidor PostgreSQL, confira [Criar um Banco de Dados do Azure para PostgreSQL no Portal do Azure](quickstart-create-server-database-portal.md).

