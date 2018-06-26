---
title: 'Início rápido: criar um Banco de Dados do Azure para servidor MySQL – CLI do Azure'
description: Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de dados do Azure para MySQL no grupo de recursos do Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 3ccf2f2e6fbad6c1a1acc4b3e827a3072d8a0cb7
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293808"
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure
Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de dados do Azure para MySQL no grupo de recursos do Azure em aproximadamente cinco minutos. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias assinaturas, escolha a que for adequada na qual existe o recurso ou onde ele é cobrado. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) usando o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL
Crie um servidor de Banco de Dados do Azure para MySQL com o comando **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)**. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

O exemplo a seguir cria um servidor no Oeste dos EUA chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `myadmin`. Este é um servidor **Gen 4** de **Uso geral** com 2 **vCores**. O nome de um servidor é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo no Azure. Substitua o `<server_admin_password>` com seu próprio valor.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>Configurar regra de firewall
Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para MySQL com o comando **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)**. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando **mysql.exe** ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço Azure MySQL. 

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substituir no endereço IP ou intervalo dos endereços IP que correspondem a onde você vai se conectar. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Se esse for o caso, você não poderá se conectar ao seu servidor enquanto o departamento de TI não abrir a porta 3306.
> 


## <a name="configure-ssl-settings"></a>Configurar definições de SSL
Por padrão, as conexões SSL entre o servidor e os aplicativos cliente são impostas. Essa configuração padrão garante a segurança de dados "em movimento" ao criptografar o fluxo de dados pela Internet. Para facilitar esse início rápido, desabilite as conexões SSL para o servidor. Desabilitar SSL não é recomendável para servidores de produção. Para saber mais, veja [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md).

O exemplo a seguir desabilita a imposição de SSL no servidor MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obter as informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando mysql.exe
Conectar-se ao seu servidor usando a ferramenta de linha de comando **mysql.exe**. Você pode baixar o MySQL [daqui](https://dev.mysql.com/downloads/) e instalá-lo em seu computador. Em vez disso, você também pode clicar no botão **Experimente** nos exemplos de código, ou no botão `>_` da barra de ferramentas superior direita no portal do Azure e iniciar o **Azure Cloud Shell**.

Digite os comandos seguintes: 

1. Conecte-se ao servidor usando a ferramenta de linha de comando **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. Exibir o status do servidor:
```sql
 mysql> status
```
Se tudo correr bem, a ferramenta de linha de comando deve gerar o seguinte texto:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conectar-se ao servidor usando a ferramenta GUI do MySQL Workbench
1.  Inicie o aplicativo MySQL Workbench no computador cliente. É possível baixar e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   ![configurar nova conexão](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Configuração** | **Valor Sugerido** | **Descrição** |
|---|---|---|
|   Nome da Conexão | Minha Conexão | Especifique um nome para essa conexão (pode ser qualquer nome) |
| Método de Conexão | escolha o Padrão (TCP/IP) | Usar o protocolo TCP/IP para se conectar ao Banco de Dados do Azure para MySQL> |
| Nome do host | mydemoserver.mysql.database.azure.com | O nome do servidor que você anotou anteriormente. |
| Porta | 3306 | A porta padrão para o MySQL é usada. |
| Nome de Usuário | myadmin@mydemoserver | O logon de administrador do servidor que você anotou anteriormente. |
| Senha | **** | Use a senha de conta de administrador que você configurou anteriormente. |

Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente.
Agora, é possível clicar na conexão para se conectar ao servidor com êxito.

## <a name="clean-up-resources"></a>Limpar recursos
Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como criar um banco de dados MySQL com a CLI do Azure](./tutorial-design-database-using-cli.md)
