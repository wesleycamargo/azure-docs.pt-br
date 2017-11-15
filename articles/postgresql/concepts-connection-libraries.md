---
title: "Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Este artigo descreve várias bibliotecas e vários drivers que os desenvolvedores podem usar ao codificar aplicativos para se conectar e consultar o Banco de Dados do Azure para PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: f371d5cd4e20096d5101fadf9066e3a135218d0b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL
Este artigo lista as bibliotecas e os drivers que os desenvolvedores podem usar para desenvolver aplicativos para conexão e consulta do Banco de Dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem usadas para se conectar ao servidor PostgreSQL são projetos externos distribuídos de forma independente. As bibliotecas listadas têm suporte em plataformas Windows, Linux e Mac para conexão ao Banco de Dados do Azure para PostgreSQL. Vários exemplos de início rápido são listados na seção Próximas etapas.

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
Leia estes guias de início rápido para saber como se conectar e consultar o Banco de Dados do Azure para PostgreSQL usando a linguagem de sua escolha:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
