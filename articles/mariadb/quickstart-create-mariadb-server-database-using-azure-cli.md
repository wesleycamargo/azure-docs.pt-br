---
title: 'Início Rápido: Criar um servidor do Banco de Dados do Azure para MariaDB – CLI do Azure'
description: Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de Dados do Azure para MariaDB no grupo de recursos do Azure.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996597"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Criar um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de Dados do Azure para MariaDB no grupo de recursos do Azure em aproximadamente cinco minutos. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias assinaturas, escolha a que for adequada na qual existe o recurso ou onde ele é cobrado. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um servidor do Banco de Dados do Azure para MariaDB
Crie um servidor de Banco de Dados do Azure para MariaDB com o comando **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)**. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
Nome | mydemoserver | Escolha um nome exclusivo que identifique o servidor de Banco de Dados do Azure para MariaDB. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
sku-name | GP_Gen5_2 | O nome da SKU. Segue a convenção {tipo de preço}_{geração de computação}_{vCores} em formato abreviado. Embaixo desta tabela há mais informações sobre o parâmetro sku-name.
backup-retention | 7 | Quanto tempo um backup deve ser retido. A unidade é dias. O intervalo é de 7 a 35. 
geo-redundant-backup | Desabilitado | Indica se os backups com redundância geográfica devem ser habilitados para este servidor ou não. Valores permitidos: habilitado, desabilitado.
location | westus | O local do Azure para o servidor.
ssl-enforcement | habilitado | Se o SSL deve ser habilitado ou não para este servidor. Valores permitidos: habilitado, desabilitado.
storage-size | 51200 | A capacidade de armazenamento do servidor (a unidade é megabytes). O tamanho de armazenamento válido é no mínimo 5.120 MB e aumenta em incrementos de 1.024 MB. Confira o documento [tipos de preço](./concepts-pricing-tiers.md) para obter mais informações sobre limites de tamanho de armazenamento. 
version | 10.2 | A versão do mecanismo principal do MariaDB.
admin-user | myadmin | O nome de usuário do logon de administrador. Não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.
admin-password | Password123 | A senha do usuário administrador. Ele deve conter entre 8 e 128 caracteres. A senha deve conter caracteres de três das categorias a seguir: letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.

O valor do parâmetro sku-name segue a convenção {camada de preços}\_{geração de cálculo}\_{vCores} como nestes exemplos:
+ `--sku-name B_Gen5_4` mapeia para Básico, Gen 5 e 4 vCores.
+ `--sku-name GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Veja a documentação das [camadas de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por camada.

O exemplo a seguir cria um servidor no Oeste dos EUA chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `myadmin`. Este é um servidor **Gen 5** de **Uso Geral** com 2 **vCores**. O nome de um servidor é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo no Azure. Substitua o `<server_admin_password>` com seu próprio valor.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Configurar regra de firewall
Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para MariaDB com o comando **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)**. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando **mysql** ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço Azure MariaDB. 

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substituir no endereço IP ou intervalo dos endereços IP que correspondem a onde você vai se conectar. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MariaDB se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Se esse for o caso, você não poderá se conectar ao seu servidor enquanto o departamento de TI não abrir a porta 3306.
> 

## <a name="configure-ssl-settings"></a>Configurar definições de SSL
Por padrão, as conexões SSL entre o servidor e os aplicativos cliente são impostas. Essa configuração padrão garante a segurança de dados "em movimento" ao criptografar o fluxo de dados pela Internet. Para facilitar esse início rápido, desabilite as conexões SSL para o servidor. Desabilitar SSL não é recomendável para servidores de produção. Para saber mais, confira [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md) (Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MariaDB).

O exemplo a seguir desabilita a imposição de SSL no servidor MariaDB.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-server-using-mysql-command-line"></a>Conecte-se ao servidor usando a ferramenta de linha de comando mysql
Conectar-se ao seu servidor usando a ferramenta de linha de comando **mysql**. É possível baixar a ferramenta de linha de comando [aqui](https://dev.mysql.com/downloads/) e instalá-la em seu computador. Em vez disso, você também pode clicar no botão **Experimente** nos exemplos de código, ou no botão `>_` da barra de ferramentas superior direita no portal do Azure e iniciar o **Azure Cloud Shell**.

Digite os comandos seguintes: 

1. Conecte-se ao servidor usando a ferramenta de linha de comando **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Exiba o status do servidor no prompt mysql>:
```sql
status
```
Se tudo correr bem, a ferramenta de linha de comando deve gerar o seguinte texto:

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-server-using-mysql-workbench"></a>Conectar-se ao servidor usando MySQL Workbench
1.  Inicie o aplicativo MySQL Workbench no computador cliente. É possível baixar e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   ![configurar nova conexão](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Configuração** | **Valor Sugerido** | **Descrição** |
|---|---|---|
|   Nome da Conexão | Minha Conexão | Especifique um nome para essa conexão (pode ser qualquer nome) |
| Método de Conexão | escolha o Padrão (TCP/IP) | Usar o protocolo TCP/IP para se conectar ao Banco de Dados do Azure para MariaDB |
| Nome do host | mydemoserver.mariadb.database.azure.com | O nome do servidor que você anotou anteriormente. |
| Porta | 3306 | A porta padrão do MariaDB é usada. |
| Nome de Usuário | myadmin@mydemoserver | O logon de administrador do servidor que você anotou anteriormente. |
| Senha | **** | Use a senha de conta de administrador que você configurou anteriormente. |

Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente.
Agora, é possível clicar na conexão para se conectar ao servidor com êxito.

## <a name="clean-up-resources"></a>Limpar recursos
Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)**.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->