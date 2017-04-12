---
title: Exemplo de script da CLI do Azure - conectar um aplicativo Web para um Banco de Dados SQL | Microsoft Docs
description: Exemplo de script da CLI do Azure - conectar um aplicativo Web para um Banco de Dados SQL
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: c576157f2c641a916f7b595fe0d2d1c0af2c0066
ms.lasthandoff: 04/04/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>Conectar um aplicativo Web a um Banco de Dados SQL

Nesse cenário, você aprenderá como criar um Banco de Dados SQL do Azure e um aplicativo Web. Em seguida, você vinculará o Banco de Dados SQL ao aplicativo Web usando configurações do aplicativo.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "Banco de dados SQL")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, o aplicativo Web, o Banco de Dados SQL e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Cria um aplicativo web do Azure no plano do Serviço de Aplicativo. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Cria um servidor de Banco de Dados SQL.  |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Cria um novo banco de dados com o servidor do Banco de Dados SQL. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Cria ou atualiza uma configuração de aplicativo para um aplicativo Web do Azure. As configurações do aplicativo são expostas como variáveis do ambiente para seu aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
