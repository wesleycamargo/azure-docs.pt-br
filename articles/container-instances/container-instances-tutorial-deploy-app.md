---
title: "Tutorial de Instâncias de Contêiner do Azure – implantar aplicativo"
description: "Tutorial de Instâncias de Contêiner do Azure – implantar aplicativo"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b651526f5ee3197e7d04accb6a87e2f10bf0791
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Implantar um contêiner em Instâncias de Contêiner do Azure

Esta é a última parte de um tutorial de três partes. Nas seções anteriores [uma imagem de contêiner foi criada ](container-instances-tutorial-prepare-app.md) e [enviada por push a um Registro de Contêiner do Azure](container-instances-tutorial-prepare-acr.md). Esta seção conclui o tutorial implantando o contêiner em Instâncias de Contêiner do Azure. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantar o contêiner do Registro de Contêiner do Azure usando a CLI do Azure
> * Exibição do aplicativo no navegador
> * Exibição dos logs de contêiner

## <a name="before-you-begin"></a>Antes de começar

Este tutorial exige a execução da CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Portanto, é recomendável uma instalação local do ambiente de desenvolvimento da CLI do Azure e do Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Implantar o contêiner usando a CLI do Azure

A CLI do Azure permite a implantação de um contêiner nas Instâncias de Contêiner do Azure por meio de um único comando. Como a imagem de contêiner é hospedada no Registro de Contêiner do Azure privado, você deve incluir as credenciais necessárias para acessá-la. Se necessário, você pode consultá-las conforme mostrado abaixo.

Servidor de logon do registro de contêiner (atualize com seu nome de registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Senha de registro de contêiner:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implantar a imagem de contêiner do registro de contêiner com uma solicitação de recurso de 1 núcleo de CPU e 1 GB de memória, execute o seguinte comando:

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure Resource Manager. Para exibir o estado da implantação, use:

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

Podemos continuar a execução desse comando até que o estado mude de *pendente* para *em execução*. Então, poderemos continuar.

## <a name="view-the-application-and-container-logs"></a>Exibir os logs do aplicativo e do contêiner

Depois que a implantação for bem-sucedida, abra o navegador no endereço IP mostrado na saída do comando a seguir:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![Aplicativo Olá, Mundo no navegador][aci-app-browser]

Você também pode exibir a saída do log do contêiner:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Saída:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu o processo de implantação dos seus contêineres nas Instâncias de Contêiner do Azure. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantar o contêiner do Registro de Contêiner do Azure usando a CLI do Azure
> * Exibição do aplicativo no navegador
> * Exibição dos logs de contêiner

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
