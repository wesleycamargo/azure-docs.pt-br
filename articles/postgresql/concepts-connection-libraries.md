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
ms.date: 06/15/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7099e26fd749820ab6869c0262e1f97d993ab7b2
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL
Este tópico lista as bibliotecas e os drivers para uso dos desenvolvedores para programação de aplicativos para se conectar e consultar o Banco de Dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem para se conectar ao servidor PostgreSQL são projetos externos distribuídos de forma independente. Elas têm suporte nas plataformas Windows, Linux e Mac. Alguns dos drivers de cliente populares estão listados:

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

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [.NET (C#)](./connect-csharp.md)

