---
title: Bibliotecas de conexão para Banco de Dados SQL | Microsoft Docs
description: Fornece links para downloads de módulos que permitem a conexão ao SQL Server e Banco de Dados SQL de uma ampla variedade de linguagens de programação do cliente.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 29a6c7107a57babb5dc81fa22146897d3958f0f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036334"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Estruturas e bibliotecas de conectividade para SQL Server

Confira nossos [Tutoriais de introdução](https://aka.ms/sqldev) para conhecer rapidamente as linguagens de programação como C#, Java, Node.js, PHP e Python. Em seguida, crie um aplicativo usando o SQL Server no Linux ou Windows ou o Docker no macOS.

A tabela a seguir lista as bibliotecas de conectividade ou *drivers* que os aplicativos cliente podem usar de uma variedade de idiomas para conectar e usar o SQL Server em execução no local ou na nuvem. Você pode usá-los no Linux, o Windows ou o Docker e usá-los para se conectar ao Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. 

| Linguagem | Plataforma | Recursos adicionais | Baixar | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Baixar](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Baixar](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver do SQL de PHP para SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Baixar](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver Node.js para SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Opções de instalação: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby Driver para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Driver ODBC Microsoft para SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Baixar](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

A tabela a seguir lista exemplos de estruturas de mapeamento relacional de objeto (ORM) e frameworks da web que os aplicativos cliente podem usar com o SQL Server em execução no local ou na nuvem. Você pode usar os frameworks no Linux, o Windows ou o Docker e usá-los para se conectar ao Banco de Dados SQL e o SQL Data Warehouse. 

| Linguagem | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Links relacionados
- [Drivers do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) que são usados para se conectar com os aplicativos cliente
- Conectar ao Banco de Dados SQL:
    - [Conectar-se ao Banco de Dados SQL usando .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Conectar-se ao Banco de Dados SQL usando o PHP](sql-database-connect-query-php.md)
    - [Conectar-se ao Banco de Dados SQL usando Node.js](sql-database-connect-query-nodejs.md)
    - [Conectar-se ao Banco de Dados SQL usando o Java](sql-database-connect-query-java.md)
    - [Conectar-se ao Banco de Dados SQL usando Phyton](sql-database-connect-query-python.md)
    - [Conectar-se ao Banco de Dados SQL usando o Ruby](sql-database-connect-query-ruby.md)
- Exemplos de código de lógica de repetição:
    - [Conectar-se de forma resiliente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Conectar-se de forma resiliente ao SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

