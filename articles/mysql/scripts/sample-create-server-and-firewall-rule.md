---
title: "Exemplos da CLI do Azure para criar um único Banco de Dados do Azure para servidor MySQL e configurar uma regra de firewall | Microsoft Docs"
description: "Este exemplo de script da CLI cria um Banco de Dados do Azure para servidor MySQL e configura uma regra de firewall no nível do servidor."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar um servidor MySQL e configurar uma regra de firewall usando a CLI do Azure
Este exemplo de script da CLI cria um Banco de Dados do Azure para servidor MySQL e configura uma regra de firewall no nível do servidor. Após o script ser executado com êxito, o servidor MySQL será acessível por todos os serviços do Azure e pelo endereço IP configurado.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Limpar implantação
 Após executar o exemplo de script, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| **Comando** | **Observações** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| az mysql server create | Cria um servidor MySQL que hospeda os bancos de dados. |
| az mysql server firewall create | Cria uma regra de firewall para permitir o acesso ao servidor e aos bancos de dados contidos nele no intervalo de endereços IP inserido. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
[Exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](../sample-scripts-azure-cli.md)

