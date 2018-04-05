---
title: Implantação contínua de um registro de contêiner do Docker com o Aplicativo Web para Contêineres – Azure | Microsoft Docs
description: Como configurar a implantação contínua de um registro de contêiner do Docker em Aplicativo Web para Contêineres.
keywords: serviço de aplicativo do azure, linux, docker, across
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implantação contínua com o Aplicativo Web para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Habilitar o recurso de implantação contínua

Habilite o recurso de implantação contínua usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e executando o comando a seguir:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

No [Portal do Azure](https://portal.azure.com/), selecione a opção **Serviço de Aplicativo** no lado esquerdo da página.

Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua do Hub do Docker.

Na página **Contêiner do Docker**, selecione **Ativar** e, em seguida, selecione **Salvar** para habilitar a implantação contínua.

![Captura de tela da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Preparar a URL do webhook

Obtenha o URL do webhook usando a [CLI do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e executando o comando a seguir:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Para a URL do webhook, o terminal a seguir é necessário: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![Captura de tela de adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Adicionar um webhook

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

1. Na página do portal do registro, selecione **Webhooks**.
2. Para criar um novo webhook, selecione **Adicionar**. 
3. No painel **Criar webhook**, nomeie o webhook. Para a URI do webhook, forneça a URL obtida na seção anterior.

Certifique-se de definir o escopo como o repositório que contém a imagem do contêiner.

![Captura de tela do webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Ao atualizar a imagem, o aplicativo da Web será atualizado automaticamente com a nova imagem.

### <a name="docker-hub"></a>Hub do Docker

Na página do Hub do Docker, selecione **Webhooks** e, em seguida, **CRIAR UM WEBHOOK**.

![Captura de tela de adição do webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para a URL do webhook, forneça a URL que você obteve anteriormente.

![Captura de tela de adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Ao atualizar a imagem, o aplicativo da Web será atualizado automaticamente com a nova imagem.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Serviço de Aplicativo do Azure no Linux](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md)
* [Criar um aplicativo Web Ruby no Serviço de Aplicativo no Linux](quickstart-ruby.md)
* [Implantar um aplicativo Web Docker/Go no Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Serviço de Aplicativo do Azure nas Perguntas Frequentes do Linux](./app-service-linux-faq.md)
* [Gerenciar Aplicativo Web para Contêineres usando a CLI do Azure](./app-service-linux-cli.md)
