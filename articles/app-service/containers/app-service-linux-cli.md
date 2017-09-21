---
title: "Gerenciar Aplicativos Web para Contêineres usando a CLI do Azure 2.0 | Microsoft Docs"
description: "Gerenciar Aplicativos Web para Contêineres usando a CLI do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, cli, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d58fab0b423b7bc1382a82f4bf308b6ad7286296
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---
# <a name="manage-web-apps-for-containers-using-azure-cli"></a>Gerenciar Aplicativos Web para Contêineres usando a CLI do Azure

Com os comandos apresentados neste artigo, é possível criar e gerenciar Aplicativos Web para Contêineres usando a CLI do Azure 2.0.
Você pode começar a usar a nova versão da CLI de duas maneiras:

* [Instalando a CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) em seu computador.
* Usando o [Azure Cloud Shell (versão prévia)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

Para criar um Plano de Serviço de Aplicativo do Linux, você pode usar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --islinux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Criar um aplicativo Web de contêiner do Docker personalizado

Para criar um aplicativo Web e configurá-lo para executar um contêiner do Docker personalizado, você pode usar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar o log de contêiner do Docker

Para ativar o log de contêiner do Docker, você pode usar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-apps-for-containers-app"></a>Alterar o contêiner do Docker personalizado para um aplicativo existente dos Aplicativos Web para Contêineres

Para alterar um aplicativo criado anteriormente, da imagem do Docker atual para uma nova imagem, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Usando imagens do Docker de um registro particular

Você pode configurar seu aplicativo para usar imagens de um registro particular. Você precisa fornecer a URL do registro, o nome de usuário e a senha. Isso pode ser feito usando o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Habilitar implantações contínuas para imagens personalizadas do Docker

Com o comando a seguir, você pode habilitar a funcionalidade de CD e obter a URL de webhook. Essa URL pode ser usada para configurar os repositórios do DockerHub ou do Registro de Contêiner do Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-apps-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Criar um aplicativo dos Aplicativos Web para Contêineres usando uma das estruturas de tempo de execução internas

Para criar um aplicativo PHP 5.6 dos Aplicativos Web para Contêineres, use o seguinte comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-apps-for-containers-app"></a>Alterar a versão da estrutura de um aplicativo existente dos Aplicativos Web para Contêineres

Para alterar um aplicativo criado anteriormente, da versão da estrutura atual para o Node.js 6.11, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar implantações do Git para seu aplicativo Web

Para configurar implantações do Git para seu aplicativo, você pode usar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Próximas etapas

* [O que são os Aplicativos Web para Contêineres do Azure?](app-service-linux-intro.md)
* [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
* [Azure Cloud Shell (versão prévia)](../../cloud-shell/overview.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação Contínua com os Aplicativos Web para Contêineres do Azure](app-service-linux-ci-cd.md)

