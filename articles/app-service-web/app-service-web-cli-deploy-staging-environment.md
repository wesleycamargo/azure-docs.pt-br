---
title: Exemplo de script da CLI do Azure - Implantar um aplicativo Web em um ambiente de preparo | Microsoft Docs
description: Exemplo de script da CLI do Azure - Implantar um aplicativo Web em um ambiente de preparo
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>Implantar um aplicativo Web em um ambiente de preparo

Este script de exemplo faz o seguinte usando a CLI do Azure 2.0: 

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure.
* Atualize o Plano do Serviço de Aplicativo para a camada padrão (mínimo de slots de implantação).
* Configure um slot de implantação denominado "preparo".
* Implante o código do aplicativo Web do GitHub para o slot de preparo.
* Abra o slot de preparo implantado no navegador para verificação.
* Agora, troque o slot de preparo para produção.

## <a name="prerequisites"></a>Pré-requisitos

* Execute `az login` para efetuar logon no Azure.
* Coloque o código do aplicativo Web em um repositório do GitHub que você possui.

> [!NOTE]
> Se você usar um repositório GitHub público que não seja de sua propriedade, o serviço de aplicativo implantará código por meio desse repositório GitHub, mas não poderá configurar a chave SSH e os webhooks necessário à implantação contínua.
>
>

## <a name="create-vm-sample"></a>Criar exemplo de VM

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Implantar um aplicativo Web em um ambiente de preparo")]

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
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Atualize um plano do Serviço de Aplicativo para dimensionar sua faixa de preço. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | Crie um slot de implantação. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Associa um aplicativo web com um repositório Mercurial ou Git. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abra um aplicativo Web em um navegador. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | Troca um slot de implantação especificado para produção. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Exemplos adicionais de script CLI para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos de CLI do Azure]().

