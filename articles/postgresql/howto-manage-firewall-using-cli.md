---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure | Microsoft Docs
description: Descreve como criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
As regras de firewall no nível de servidor permitem que os administradores gerenciem o acesso a um Banco de Dados SQL do Azure para servidor PostgreSQL de um endereço IP específico ou intervalo de endereços IP. Usando comandos convenientes da CLI do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras de firewall para gerenciar o servidor. Para obter uma visão geral dos firewalls do Banco de Dados do Azure para PostgreSQL, confira [Regras de firewall do servidor de Banco de Dados do Azure para PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Banco de Dados do Azure para servidor e banco de dados PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalado

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurar regras de firewall para o Banco de Dados do Azure para PostgreSQL
O comando **az postgres server firewall-rule** é usado na CLI do Azure para criar, excluir, listar, exibir e atualizar regras de firewall. Isso 

## <a name="login-to-azure-and-list-servers"></a>Fazer logon no Azure e listar os servidores
Conecte com segurança à CLI do Azure com sua conta do Azure. Use o comando **az login** para fazer isso.
1. Execute o comando a seguir na linha de comando.
```azurecli
az login
```
Esse comando retornará um código para usar na próxima etapa.

2. Use um navegador da Web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código.

3. No prompt, entre utilizando suas credenciais do Azure.

4. Após a autorização do logon, uma lista de assinaturas será impressa no console.
Copie a id da assinatura desejada para definir a assinatura atual a ser usada da pra frente.
```azurecli
az account set --subscription {your subscription id}
```
5. Liste os Bancos de Dados do Azure para servidores PostgreSQL para sua assinatura e grupo de recursos, se você não tiver certeza dos nomes.

```azurecli
az postgres server list --resource-group myresourcegroup
```
Anote o atributo name na lista, que será usado para especificar em qual servidor PostgreSQL trabalhar. Se for necessário, confirme os detalhes desse servidor usando o atributo name para confirmar se o nome está correto:

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>Listar regras de firewall 
Usando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Observe que o atributo de nome do servidor é especificado na opção **-server** e não na opção **-name**.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
A saída listará as regras, se houver, por padrão, no formato JSON. Você pode usar a opção **-output table** para obter um formato de tabela mais legível como saída.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Criar uma regra de firewall
Usando o nome de servidor do Banco de Dados do Azure para PostgreSQL e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Forneça um nome para a regra, os IPs inicial e final para a regra abranger um intervalo de endereços IP aos quais permitir o acesso.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Para permitir o acesso de um endereço IP único, fornece o mesmo endereço como o IP Inicial e IP Final, como neste exemplo.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você criou, em formato JSON por padrão. Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="update-firewall-rule"></a>Atualizar uma regra de firewall 
Usando o nome de servidor do Banco de Dados do Azure para PostgreSQL e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada, e os atributos IP inicial e IP final para atualizar.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você atualizou, em formato JSON por padrão. Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.
> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra de firewall
Usando o nome de servidor do Banco de Dados do Azure para PostgreSQL e o nome do grupo de recursos, mostre os detalhes da regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você especificou, em formato JSON por padrão. Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="delete-firewall-rule"></a>Excluir regra de firewall
Usando o nome de servidor do Banco de Dados do Azure para PostgreSQL e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Forneça o nome da regra de firewall existente.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro retornará.

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, você pode usar um navegador da Web para [Criar e gerenciar as regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-manage-firewall-using-portal.md)
- Entenda mais sobre [Regras de firewall do servidor de Banco de Dados do Azure para PostgreSQL](concepts-firewall-rules.md)
- Para obter ajuda com a conexão com um Banco de Dados para servidor PostgreSQL, veja [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md)

