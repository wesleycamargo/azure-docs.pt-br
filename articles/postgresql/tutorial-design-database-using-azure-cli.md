---
title: Projetar seu primeiro Banco de Dados do Azure para PostgreSQL usando a CLI do Azure | Microsoft Docs
description: Este tutorial mostra como criar seu primeiro Banco de Dados do Azure para PostgreSQL usando a CLI do Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Criar seu primeiro Banco de Dados do Azure para PostgreSQL usando a CLI do Azure 
Neste tutorial, você usará a CLI (interface de linha de comando) do Azure e outros utilitários para aprender a:
> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para o PostgreSQL
> * Configurar o firewall do servidor
> * Useo utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.
```azurecli
az login
```

Se tiver várias assinaturas, escolha a que for adequada na qual existe o recurso ou onde ele é cobrado. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#set).
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um [Banco de Dados do Azure para PostgreSQL](overview.md) usando o comando **az postgres server create**. Um servidor contém um grupo de bancos de dados gerenciados conjuntamente. 

O exemplo a seguir cria um servidor chamado `mypgserver-20170401` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `mylogin`. O nome de um servidor é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo no Azure. Substitua o `<server_admin_password>` com seu próprio valor.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste início rápido. Lembre-se ou registre essas informações para o uso posterior.

Por padrão, o banco de dados **postgres** é criado em seu servidor. O [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado a uso por usuários, utilitários e aplicativos de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Crie uma regra de firewall no nível de servidor do PostgreSQL do Azure com o comando **az postgres server firewall-rule create**. Uma regra de firewall de nível de servidor permite que um aplicativo externo, tal como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), conecte-se ao servidor por meio do firewall do serviço Azure PostgreSQL. 

Você pode definir uma regra de firewall que abranja um intervalo de IP aos quais você possa se conectar de sua rede. O exemplo a seguir usa **az postgres server firewall-rule create** para criar uma regra de firewall `AllowAllIps` para um intervalo de endereços IP. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
>

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao seu servidor, você precisa fornecer credenciais de acesso e informações de host.
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

O resultado está no formato JSON. Anote o **administratorLogin** e o **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Conectar-se ao Banco de Dados do Azure para o banco de dados PostgreSQL usando psql

Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao servidor PostgreSQL do Azure.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para servidor PostgreSQL
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Quando já estiver conectado ao servidor, crie um banco de dados em branco no prompt.
```bash
CREATE DATABASE mypgsqldb;
```

3.  No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado:
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados
Agora que você sabe como se conectar ao Banco de Dados do Azure para o PostgreSQL, podemos falar sobre como concluir algumas tarefas básicas.

Primeiro, criamos uma tabela e a carregamos com alguns dados. Vamos criar uma tabela que rastreia informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Você agora pode ver a tabela recém-criada na lista de tabelas digitando:
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas
Agora que temos uma tabela, podemos inserir alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Você tem agora duas linhas de dados de exemplo na tabela criada anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para recuperar as informações da tabela do banco de dados. 
```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados nas tabelas
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada à medida que você recupera os dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo
Imagine que você excluiu acidentalmente uma tabela. Isso é algo que você não pode se recuperar facilmente. O Banco de Dados do Azure para PostgreSQL permite que você volte até qualquer ponto no tempo (até os últimos 7 dias (Basic) e 35 dias (Standard)) e restaure esse ponto no tempo em um novo servidor. Use esse novo servidor para recuperar seus dados excluídos. As etapas a seguir restauram o servidor de exemplo para um ponto anterior à adição da tabela.

Para a Restauração, você precisa das seguintes informações:
- **Ponto de restauração**: selecione um ponto no tempo anterior à alteração do servidor. Deve ser maior ou igual ao valor de backup mais antigo do banco de dados de origem.
- **Servidor de destino**: forneça um novo nome de servidor no qual você deseja restaurar
- **Servidor de origem**: forneça o nome do servidor do qual você quer fazer a restauração
- **Local**: não é possível selecionar a região; por padrão, ela é igual ao servidor de origem
- **Tipo de preço**: não é possível alterar esse valor ao restaurar um servidor. Ele é igual ao servidor de origem. 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

Para restaurar o servidor para [restaurar em um ponto no tempo](./howto-restore-server-portal.md) anterior à exclusão das tabelas. A restauração de um servidor para um ponto diferente no tempo cria um novo servidor duplicado como o servidor original a partir do ponto no tempo especificado por você, desde que esteja dentro do período de retenção de sua [camada de serviço](./concepts-service-tiers.md).

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como usar a CLI (interface de linha de comando) do Azure e outros utilitários para aprender a:
> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para o PostgreSQL
> * Configurar o firewall do servidor
> * Useo utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Em seguida, aprenda como usar o portal do Azure para executar tarefas semelhantes, consulte este tutorial: [Criar seu primeiro Banco de Dados do Azure para PostgreSQL usando o portal do Azure](tutorial-design-database-using-azure-portal.md)

