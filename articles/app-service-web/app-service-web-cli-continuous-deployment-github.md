---
title: Exemplo de Script CLI do Azure - Implantar continuamente o aplicativo Web do GitHub | Microsoft Docs
description: Exemplo de Script CLI do Azure - Implantar continuamente o aplicativo Web do GitHub
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>Implantar continuamente o aplicativo Web do GitHub

Este script de exemplo faz o seguinte usando a CLI do Azure 2.0: 

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure. 
* Implante o código do aplicativo Web do GitHub.
* Exiba o aplicativo Web implantado no navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Execute `az login` para efetuar logon no Azure.
* Coloque o código do aplicativo Web em um repositório do GitHub que você possui.

> [!NOTE]
> Se você usar um repositório GitHub público que não seja de sua propriedade, o serviço de aplicativo implantará código por meio desse repositório GitHub, mas não poderá configurar a chave SSH e os webhooks necessário à implantação contínua.
>
>

## <a name="create-vm-sample"></a>Criar exemplo de VM

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-github/deploy-github.sh "Implantar continuamente o aplicativo Web do GitHub")]

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
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Cria um aplicativo web. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Associa um aplicativo web com um repositório Mercurial ou Git. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abra um aplicativo Web em um navegador. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Exemplos adicionais de script CLI para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos de CLI do Azure]().

