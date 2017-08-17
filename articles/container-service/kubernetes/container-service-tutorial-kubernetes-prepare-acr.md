---
title: "Tutorial do Serviço de Contêiner do Azure – preparar o ACR | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – preparar o ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: f8346fd6bd78c32cd67a2f988046cad2a8fc2455
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Implantar e usar o Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) é um registro privado baseado no Azure, para imagens de contêiner do Docker. Este tutorial, parte dois de sete, demonstra a implantação de uma instância de Registro de Contêiner do Azure e envia por push uma imagem de contêiner. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantando uma instância de ACR (Registro de Contêiner do Azure)
> * Marcando uma imagem de contêiner para ACR
> * Carregando da imagem para ACR

Nos próximos tutoriais, essa instância do ACR será integrada a um cluster do Kubernetes de Serviço de Contêiner do Azure, para executar as imagens de contêiner com segurança. 

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), uma imagem de contêiner foi criada para um aplicativo de Votação do Azure simples. Neste tutorial, essa imagem é enviada por push para um Registro de Contêiner do Azure. Se você não tiver criado a imagem do aplicativo de Votação do Azure, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). Como alternativa, as etapas descritas aqui funcionam com qualquer imagem de contêiner.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#create). O nome de um registro de contêiner **deve ser exclusivo**. No exemplo a seguir, utilizamos o nome myContainerRegistry007.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

Durante o restante deste tutorial, utilizamos "acrname" como um espaço reservado para o nome do registro de contêiner escolhido.

## <a name="get-registry-information"></a>Obter informações do registro 

Depois que a instância do ACR for criada, o nome, o nome do servidor de logon e a senha de autenticação serão necessários. O código a seguir retorna cada um desses valores. Anote cada valor, eles serão referenciados ao longo deste tutorial.  

Servidor de logon do registro de contêiner (atualização com o nome do registro):

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

Senha de registro de contêiner:

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value
```

## <a name="container-registry-login"></a>Logon no registro de contêiner

Você deverá entrar na instância do ACR antes de enviar imagens por push a ele. Use o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) para concluir a operação. Ao executar o logon do docker, você precisa fornecer o nome do servidor de logon do ACR e as credenciais do ACR.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-images"></a>Marcar imagens de contêiner

Cada imagem de contêiner precisa ser marcada com o nome do registro loginServer. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para consultar uma lista de imagens atuais, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Marque a imagem *azure-vote-front* com o loginServer do registro de contêiner. Além disso, adicione `:redis-v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Quando marcada, execute [docker images] (https://docs.docker.com/engine/reference/commandline/images/) para verificar a operação.

```bash
docker images
```

Saída:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Enviar a imagem *azure-vote-front* por push ao registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Isso leva alguns minutos para ser concluído.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository#list). Atualize o comando com o nome da instância do ACR.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Saída:

```azurecli
Result
----------------
azure-vote-front
```

E, em seguida, para ver as marcas de uma imagem específica, use o comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Saída:

```azurecli
Result
--------
redis-v1
```

Na conclusão do tutorial, a imagem de contêiner foi armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem será implantada do ACR para um cluster Kubernetes em tutoriais subsequentes.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um Registro de Contêiner do Azure foi preparado para ser usado em um cluster do Kubernetes do ACS. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância de Registro de Contêiner do Azure
> * Marcando uma imagem de contêiner para ACR
> * Carregando a imagem para ACR

Avance para o próximo tutorial para saber mais sobre a implantação de um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
