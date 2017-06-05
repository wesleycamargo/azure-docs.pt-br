---
title: "Script da CLI do Azure – dimensionar o Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – dimensionar o Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica."
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
ms.date: 04/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 36134ca1a899c25ab896577815ce9108cae0d563
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitore e dimensione um único servidor PostgreSQL usando a CLI do Azure
Este exemplo de script da CLI dimensiona um único Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica. 

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az postgres server update \
--resource-group myresourcegroup \
--name mypgserver-20170401 \
--compute-units 100
```

## <a name="clean-up-deployment"></a>Limpar implantação
Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
- Exemplos adicionais de scripts da CLI do PostgreSQL para o Banco de Dados do Azure podem ser encontrados na [Documentação do Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md).
- Para obter mais informações sobre dimensionamento, consulte [Camadas de serviço](../concepts-service-tiers.md) e [Unidades de computação e unidades de armazenamento](../concepts-compute-unit-and-storage.md).
