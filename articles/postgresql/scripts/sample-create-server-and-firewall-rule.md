---
title: "Script da CLI do Azure – criar um Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – cria um Banco de Dados do Azure para servidor PostgreSQL e configura uma regra de firewall no nível do servidor."
services: postgresql
author: salonisonpal
ms.author: salonis
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
ms.openlocfilehash: 0f61358aa655a44ac7982c80eb1d796667c47bf4
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Crie um Banco de Dados do Azure para servidor PostgreSQL e configure uma regra de firewall usando a CLI do Azure
Este exemplo de script da CLI cria um Banco de Dados do Azure para servidor PostgreSQL e configura uma regra de firewall no nível do servidor. Depois que o script tiver sido executado com êxito, o servidor PostgreSQL poderá ser acessado de todos os serviços do Azure e o endereço IP configurado.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az postgres server firewall-rule create \
--resource-group myresourcegroup \
--server mypgserver-20170401 \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Limpar implantação
Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
- Exemplos adicionais de scripts da CLI do PostgreSQL para o Banco de Dados do Azure podem ser encontrados na [Documentação do Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md).

