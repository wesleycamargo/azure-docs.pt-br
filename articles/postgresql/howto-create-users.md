---
title: "Criar usuários em um Banco de Dados do Azure para o servidor PostgreSQL"
description: "Este artigo descreve como você pode criar novas contas de usuário para interagir com um servidor de Banco de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Criar usuários em um Banco de Dados do Azure para o servidor PostgreSQL 
Este artigo descreve como você pode criar usuários em um servidor de Banco de Dados do Azure para PostgreSQL.

## <a name="the-server-admin-account"></a>A conta do administrador do servidor
Quando foi criado o Banco de Dados do Azure para PostgreSQL, você forneceu um nome de usuário de administrador de servidor e uma senha. Para saber mais, você pode seguir o [Início Rápido](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Como o nome de usuário administrador do servidor é um nome personalizado, você pode localizar o nome de usuário administrador do servidor escolhido no portal do Azure.

O banco de dados do Azure para o servidor PostgreSQL é criado com as 3 funções padrão definidas. Você pode ver essas funções executando o comando: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- seu usuário administrador do servidor

O usuário administrador do servidor é um membro da função azure_pg_admin. No entanto, a conta do administrador do servidor não faz parte da função azure_superuser. Como esse serviço é um serviço gerenciado de PaaS, somente a Microsoft faz parte da função de superusuário. 

O mecanismo PostgreSQL usa privilégios para controlar o acesso a objetos de banco de dados, conforme discutido na [documentação do produto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). No Banco de Dados do Azure para PostgreSQL, o usuário administrador do servidor tem estes privilégios: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

A conta de usuário administrador do servidor pode ser usada para criar usuários adicionais e conceder a esses usuários acesso à função azure_pg_admin. Além disso, a conta de administrador do servidor pode ser usada para criar usuários e funções com menos privilégios que têm acesso aos banco de dados e esquemas individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar usuários administradores adicionais no Banco de Dados do Azure para PostgreSQL
1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta preferencial do cliente, como pgAdmin ou psql.
   Se não tiver certeza de como se conectar, consulte [Conectar-se ao Banco de Dados PostgreSQL usando o psql no Cloud Shell](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Edite e execute o código SQL a seguir. Substitua seu novo nome de usuário pelo valor de espaço reservado < new_user > e a senha de espaço reservado com sua própria senha forte. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar usuários de banco de dados no Banco de Dados do Azure para PostgreSQL

1. Obter o nome de usuário do administrador e as informações de conexão.
   Para conectar o servidor de banco de dados, você precisa do nome do servidor completo e as credenciais de entrada do administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **Visão Geral** ou na página **Propriedades** do servidor no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao seu servidor de banco de dados. Use sua ferramenta preferencial do cliente, como pgAdmin ou psql.

3. Edite e execute o código SQL a seguir. Substitua o valor de espaço reservado `<db_user>` com seu novo nome de usuário pretendido e o valor de espaço reservado `<newdb>` com seu próprio nome de banco de dados. Substitua a senha de espaço reservado pela sua própria senha forte. 

   Essa sintaxe de código sql cria um novo banco de dados nomeado testdb para fins de exemplo. Em seguida, ele cria um novo usuário no serviço do PostgreSQL e concede privilégios de conexão para o novo banco de dados para o usuário. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. Usando uma conta de administrador, você precisará conceder privilégios adicionais para proteger os objetos no banco de dados. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para obter mais detalhes sobre as funções de banco de dados e os privilégios. Por exemplo:  
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do psql. Com este comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome de servidor, o nome do banco de dados e o nome de usuário.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Próximas etapas
Abra o firewall para os endereços IP das máquinas os novos usuários para que ele possa se conectar: [Criar e gerenciar regras de firewall para o Banco de Dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para saber mais sobre gerenciamento de contas de usuário, consulte a documentação do produto do PostgreSQL para [Funções e Privilégios do Banco de Dados](https://www.postgresql.org/docs/current/static/user-manag.html), [Sintaxe de GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) e [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html).
