---
title: Compatibilidade de drivers MySQL e de ferramentas de gerenciamento
description: Esse artigo descreve os drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 11/21/2018
ms.openlocfilehash: 4232e1850a1354fe247258d46b280e1bfa653972
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290817"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL
Esse artigo descreve os drivers e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL.

## <a name="mysql-drivers"></a>Drivers MySQL
O Banco de Dados do Azure para MySQL usa a edição de comunidade mais popular do mundo do banco de dados MySQL. Portanto, ele é compatível com uma ampla variedade de drivers e linguagens de programação. A meta é dar suporte às três versões mais recentes de drivers MySQL e os esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e usabilidade dos drivers MySQL continuam. Na tabela a seguir, há uma lista de drivers que foram testados e que são compatíveis com o Banco de Dados do Azure para MySQL 5.6 e 5.7:

| **Driver** | **Links** | **Versões compatíveis** | **Versões incompatíveis** | **Observações** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Para a conexão PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO defina a opção ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` como false.|
| .Net | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação do Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e posterior | 0.26.5 e anterior | |
| Conector MySQL/NET | [Conector MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Um bug de codificação pode causar falha em alguns sistemas não - UTF8 Windows nas conexões. |
| Nodejs |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` do NPM | 2.15 | 2.14.1 e anterior | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1,3 | 1.2 e anterior | Use allowNativePasswords=true na cadeia de conexão |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e anterior | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e anterior | |

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
