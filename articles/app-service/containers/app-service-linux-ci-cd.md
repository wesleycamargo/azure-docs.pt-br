---
title: Implantação contínua de um registro de contêiner do Docker com o Aplicativo Web para Contêineres – Azure | Microsoft Docs
description: Como configurar a implantação contínua de um registro de contêiner do Docker em Aplicativo Web para Contêineres.
keywords: serviço de aplicativo do azure, linux, docker, across
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 20ca63b7126a6800538129115ff339308c11d8c5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867010"
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

Na página **Configurações do Contêiner**, selecione **Ativar** e, em seguida, selecione **Salvar** para habilitar a implantação contínua.

![Captura de tela da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Preparar a URL do webhook

Obtenha o URL do webhook usando a [CLI do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e executando o comando a seguir:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Anote a URI do webhook. Você precisará dele na próxima seção.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![Captura de tela de adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Adicionar um webhook

Para adicionar um webhook, siga as etapas nestes guias:

- [Registro de Contêiner do Azure](../../container-registry/container-registry-webhook.md) usando a URL de webhook
- [Webhooks para o Hub do Docker](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Serviço de Aplicativo do Azure no Linux](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md)
* [Criar um aplicativo Web Ruby no Serviço de Aplicativo no Linux](quickstart-ruby.md)
* [Implantar um aplicativo Web Docker/Go no Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Serviço de Aplicativo do Azure nas Perguntas Frequentes do Linux](./app-service-linux-faq.md)
* [Gerenciar Aplicativo Web para Contêineres usando a CLI do Azure](./app-service-linux-cli.md)
