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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: ec5e22bfacc12a89f1fb5882487df4829369777c
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>Conectar um aplicativo Web a um Banco de Dados SQL

Nesse cenário, você aprenderá como criar um Banco de Dados SQL do Azure e um aplicativo Web. Em seguida, você vinculará o Banco de Dados SQL ao aplicativo Web usando configurações do aplicativo.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "Banco de dados SQL")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, o aplicativo Web, o Banco de Dados SQL e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | Cria um aplicativo Web do Azure. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Cria um servidor de Banco de Dados SQL.  |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Cria um novo banco de dados com o servidor do Banco de Dados SQL. |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#set) | Cria ou atualiza uma configuração de aplicativo para um aplicativo Web do Azure. As configurações do aplicativo são expostas como variáveis do ambiente para seu aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).

