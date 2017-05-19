---
title: "Início rápido: criar um servidor do Banco de Dados do Azure para MySQL – CLI do Azure | Microsoft Docs"
description: "Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de dados do Azure para MySQL no grupo de recursos do Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 574299dd64120d75a1a36cb2ded0fdd269292570
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure
Este início rápido descreve como usar a CLI do Azure para criar um servidor de Banco de dados do Azure para MySQL no grupo de recursos do Azure em aproximadamente cinco minutos. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts.

Para concluir este início rápido, certifique-se de que a versão mais recente da [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) esteja instalada. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```
Siga as instruções do prompt de comando para abrir https://aka.ms/devicelog em seu navegador e depois insira o código gerado no **prompt de comando**.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) usando o comando [az group create](https://docs.microsoft.com/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

O exemplo a seguir cria um grupo de recursos denominado `mycliresource` no local `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL
Crie um servidor de Banco de Dados do Azure para MySQL com o comando **az mysql server create**. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

O exemplo a seguir cria um servidor de Banco de Dados do Azure para MySQL localizado em `westus` no grupo de recursos `mycliresource` com o nome `mycliserver`. O servidor tem um logon de administrador chamado `myadmin` e a senha `Password01!`. O servidor é criado com o nível de desempenho **Básico** e **50** unidades de computação compartilhadas entre todos os bancos de dados no servidor. Você pode escalar verticalmente a computação e o armazenamento dependendo das necessidades do aplicativo.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Configurar regra de firewall
Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para MySQL com o comando **az mysql server firewall-rule create**. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando **mysql.exe** ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço Azure MySQL. 

O exemplo a seguir cria uma regra de firewall para um intervalo de endereços predefinidos, que, neste exemplo, é o intervalo inteiro possível de endereços IP.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configurar definições de SSL
Por padrão, as conexões SSL entre o servidor e os aplicativos cliente são impostas.  Isso garante a segurança de dados "em movimento" ao criptografar o fluxo de dados pela Internet.  Para facilitar esse início rápido, vamos desabilitar conexões SSL para o servidor.  Isso não é recomendável para servidores de produção.  Para obter mais detalhes, consulte [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md).

O exemplo a seguir desabilita a imposição de SSL no servidor MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obter as informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando mysql.exe
Para conectar-se ao servidor usando a ferramenta de linha de comando **mysql.exe**, certifique-se de que o MySQL esteja instalado em seu computador.  Você pode baixar o MySQL [aqui](https://dev.mysql.com/downloads/).

Abra o prompt de comando e insira o seguinte: 

1. Conecte-se ao servidor usando a ferramenta de linha de comando **mysql**:
```dos
 mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

2. Exibir o status do servidor:
```dos
 mysql> status
```
Se tudo correr bem, a ferramenta de linha de comando deve gerar o seguinte:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
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
Connection:             mycliserver.database.windows.net via TCP/IP
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
> Para obter informações sobre outros comandos, consulte [Manual de Referência do MySQL 5.6 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conectar-se ao servidor usando a ferramenta GUI do MySQL Workbench
1.    Inicie o aplicativo MySQL Workbench no computador cliente. É possível baixar e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.    Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

| **Parâmetros** | **Descrição** |
|----------------|-----------------|
|    *Nome da Conexão* | especifique um nome para essa conexão (isso pode ser qualquer coisa) |
| *Método de Conexão* | escolha o Padrão (TCP/IP) |
| *Nome do host* | mycliserver.database.windows.net (NOME DO SERVIDOR que você anotou anteriormente) |
| *Porta* | 3306 |
| *Nome de Usuário* | myadmin@mycliserver(LOGON DE ADMINISTRADOR DO SERVIDOR que você anotou anteriormente) |
| *Senha* | você pode armazenar a senha da conta do administrador no cofre |

![configurar nova conexão](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

3.    Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente.

4.    Agora, é possível clicar na conexão recém-criada para se conectar ao servidor com êxito.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um Banco de Dados MySQL com a CLI do Azure](./tutorial-design-database-using-cli.md).

