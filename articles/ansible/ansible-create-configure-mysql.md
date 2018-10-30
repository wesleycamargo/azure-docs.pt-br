---
title: Criar e configurar um servidor do Banco de Dados do Azure para MySQL usando o Ansible (versão prévia)
description: Saiba como usar o Ansible para criar e configurar um servidor do Banco de Dados do Azure para MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, mysql, banco de dados
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: b549aeaf24bd774245ee1f2ff6924ac1f6dbeee3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427889"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible-preview"></a>Criar e configurar um servidor do Banco de Dados do Azure para MySQL usando o Ansible (versão prévia)
O [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) é um serviço gerenciado usado para executar, gerenciar e dimensionar Bancos de Dados MySQL altamente disponíveis na nuvem. O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. 

Este início rápido mostra como usar o Ansible para criar um servidor do Banco de Dados do Azure para MySQL e configurar sua regra de firewall. Você pode concluir essas tarefas em aproximadamente cinco minutos usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os guias estratégicos de exemplo contidos neste tutorial. Você pode instalar a versão RC do Ansible 2.7 executando `sudo pip install ansible[azure]==2.7.0rc2`. Após o lançamento do Ansible 2.7, você não precisará especificar a versão aqui porque a versão padrão será a 2.7.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.  

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na localização **eastus**:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Salve o guia estratégico anterior como **rg.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Criar um banco de dados e um servidor MySQL
O exemplo a seguir cria um servidor MySQL chamado **mysqlserveransible** e um Banco de Dados do Azure para MySQL chamado **mysqldbansible**. Trata-se de um servidor Gen 5 de Uso Básico com um vCore. 

O valor de **mysqlserver_name** deve ser exclusivo. Para entender os valores válidos por região e por tipo, consulte a [documentação sobre tipos de preços](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Substitua `<server_admin_password>` por uma senha.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Salve o guia estratégico anterior como **mysql_create.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall
Uma regra de firewall de nível de servidor permite que um aplicativo externo se conecte ao servidor por meio do firewall do serviço Azure MySQL. Um exemplo de um aplicativo externo é a ferramenta de linha de comando **mysql** ou o MySQL Workbench.
O exemplo a seguir cria uma regra de firewall chamada **extenalaccess**, que permite conexões de qualquer endereço IP externo. 

Insira seus próprios valores de **startIpAddress** e **endIpAddress**. Use o intervalo de endereços IP que corresponde ao local de origem da conexão. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Nesse caso, só será possível se conectar a seu servidor se seu departamento de TI abrir a porta 3306.
> 

Aqui, o módulo **azure_rm_resource** é usado para executar essa tarefa. Ele permite o uso direto da API REST.

Salve o guia estratégico anterior como **mysql_firewall.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando
Você pode [baixar o MySQL](https://dev.mysql.com/downloads/) e instalá-lo em seu computador. Em vez disso, você pode selecionar o botão **Experimente** nos exemplos de código, ou o botão **>_** da barra de ferramentas superior direita no portal do Azure e iniciar o **Azure Cloud Shell**.

Insira os seguintes comandos: 

1. Conecte-se ao servidor usando a ferramenta de linha de comando do **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Exiba o status do servidor:
```sql
 mysql> status
```

Se tudo correr bem, a ferramenta de linha de comando deve gerar o seguinte texto:

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>Usando fatos para consultar servidores MySQL
O exemplo a seguir consulta os servidores MySQL em **myResourceGroup** e, posteriormente, todos os bancos de dados nos servidores:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Salve o guia estratégico anterior como **mysql_query.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook mysql_query.yml
```

Em seguida, você verá a seguinte saída para o servidor MySQL: 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Você também verá a seguinte saída para o banco de dados MySQL:
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar desses recursos, poderá excluí-los executando o exemplo a seguir. Ele exclui um grupo de recursos chamado **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Salve o guia estratégico anterior como **rg_delete.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook rg_delete.yml
```

Se você quiser excluir apenas o servidor MySQL criado recentemente, execute o exemplo abaixo:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Salve o guia estratégico anterior como **mysql_delete.yml**. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
