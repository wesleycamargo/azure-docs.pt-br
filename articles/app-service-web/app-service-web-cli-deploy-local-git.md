---
title: "Exemplo de script da CLI do Azure - Implantar um aplicativo Web de um repositório Git local | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Implantar um aplicativo Web de um repositório Git local"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>Implantar um aplicativo Web de um repositório Git local

Este script de exemplo faz o seguinte usando a CLI do Azure 2.0: 

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure.
* Implante o código do aplicativo Web de um repositório Git local.
* Exiba o aplicativo Web implantado no navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Execute `az login` para efetuar logon no Azure.
* Confirme o código do aplicativo Web em um repositório Git local.

## <a name="create-vm-sample"></a>Criar exemplo de VM

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "Implantar um aplicativo Web de um repositório Git local")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, o aplicativo Web e todos os recursos relacionados.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Cria um aplicativo Web |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | Cria uma configuração de controle de origem para um repositório Git local. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abra um aplicativo Web em um navegador. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Exemplos adicionais de script CLI para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos de CLI do Azure]().
