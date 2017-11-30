---
title: Compatibilidade de drivers MySQL e de ferramentas de gerenciamento | Microsoft Docs
description: "Drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 1042f7919b8761bdbc23ae19871703c53dff28f0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2017
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL
Esse artigo descreve os drivers e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL.

## <a name="mysql-drivers"></a>Drivers MySQL
O Banco de Dados do Azure para MySQL usa a edição de comunidade mais popular do mundo do banco de dados MySQL. Portanto, ele é compatível com uma ampla variedade de drivers e linguagens de programação. A meta é dar suporte às três versões mais recentes de drivers MySQL e os esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e usabilidade dos drivers MySQL continuam. Na tabela a seguir, há uma lista de drivers que foram testados e que são compatíveis com o Banco de Dados do Azure para MySQL 5.6 e 5.7:

| **Driver** | **Links** | **Versões compatíveis** | **Versões não compatíveis** | **Observações** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5,3 | Para a conexão PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO defina a opção ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` como false.|
| .Net | [MySqlConnector no Github]: https://github.com/mysql-net/MySqlConnector/releases <br> [Pacote de instalação do Nuget]:<br> https://www.nuget.org/packages/MySqlConnector/ | 0.27 e posterior | 0.26.5 e anterior | |
| Nodejs |  [MySQLjs no Github]:<br> https://github.com/mysqljs/mysql/releases <br> [Pacote de instalação do NPM]:<br> Execute "npm install mysql" no NPM | 2.15 | 2.14.1 e anterior | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1,3 | 1.2 e anterior | Use allowNativePasswords=true na cadeia de conexão |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e anterior | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 e anterior | |

## <a name="management-tools"></a>Ferramentas de gerenciamento
A vantagem de compatibilidade se estende para as ferramentas de gerenciamento de banco de dados também. Suas ferramentas existentes devem continuar trabalhando com o Banco de Dados do Azure para MySQL, contanto que a manipulação do banco de dados seja operada dentro dos limites das permissões de usuário. Três ferramentas comuns de gerenciamento de banco de dados que foram testadas e identificadas como compatíveis com o Banco de Dados do Azure para MySQL 5.6 e 5.7 estão listadas na tabela a seguir:

|                                     | **MySQL Workbench 6.x e superior** | **Navicat 12** | **PHPMyAdmin 4.x e superior** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Criar, atualizar, ler, gravar, excluir | X | X | X |
| Conexão SSL | X | X | X |
| Preenchimento automático da consulta SQL | X | X |  |
| Importar e exportar dados | X | X | X |
| Exportar para vários formatos | X | X | X |
| Backup e restauração |  | X |  |
| Exibir parâmetros do servidor | X | X | X |
| Exibir conexões de cliente | X | X | X |
