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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Implantar e usar o Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) é um registro privado baseado no Azure, para imagens de contêiner do Docker. Este tutorial demonstra a implantação de uma instância do Registro de Contêiner do Azure e o envio de imagens de contêiner a ele. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantando uma instância do Registro de Contêiner do Azure
> * Marcando imagens de contêiner para o ACR
> * Carregando imagens no ACR

Nos próximos tutoriais, essa instância do ACR será integrada a um cluster do Kubernetes de Serviço de Contêiner do Azure, para executar as imagens de contêiner com segurança. 

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), as imagens de contêiner foram criadas para um aplicativo simples de Votação do Azure. Neste tutorial, essas imagens serão enviadas por push para um Registro de Contêiner do Azure. Se você não tiver criado as imagens do aplicativo de Votação do Azure, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). Como alternativa, as etapas descritas aqui funcionam com qualquer imagem de contêiner.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#create). O nome de um registro de contêiner **deve ser exclusivo**. Usando o exemplo a seguir, atualize o nome com alguns caracteres aleatórios.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>Obter informações do ACR 

Depois que a instância do ACR for criada, o nome, o nome do servidor de logon e a senha de autenticação serão necessários. O código a seguir retorna cada um desses valores. Anote cada valor, eles serão referenciados ao longo deste tutorial.  

Nome e servidor de logon do ACR:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

Senha do ACR – atualize com o nome do ACR.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Logon no registro de contêiner

Você deverá entrar na instância do ACR antes de enviar imagens por push a ele. Use o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) para concluir a operação. Ao executar o logon do docker, você precisa fornecer o nome do servidor de logon do ACR e as credenciais do ACR.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-images"></a>Marcar imagens de contêiner

Cada imagem de contêiner precisa ser marcada com o nome do `loginServer` do registro. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para ver uma lista de imagens atuais, use o comando `docker images`.

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

Marque a imagem *azure-vote-front* com o loginServer do registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Essa marca indica o número de versão da imagem.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Repita o comando com a imagem *azure-vote-back*.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Depois de marcar, execute `docker images` para verificar a operação.

```bash
docker images
```

Saída:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>Envio imagens por push para o ACR

Enviar a imagem *azure-vote-front* por push ao registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente. Isso leva alguns minutos para ser concluído.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Faça o mesmo para a imagem *azure-vote-back*.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>Listar imagens no ACR 

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository#list). Atualize o comando com o nome da instância do ACR.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Saída:

```azurecli
Result
----------------
azure-vote-back
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
v1
```

Na conclusão do tutorial, as duas imagens de contêiner foram armazenadas em uma instância privada do Registro de Contêiner do Azure. Essas imagens serão implantadas do ACR a um cluster do Kubernetes nos próximos tutoriais.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um Registro de Contêiner do Azure foi preparado para ser usado em um cluster do Kubernetes do ACS. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância do Registro de Contêiner do Azure
> * Marcando imagens de contêiner para o ACR
> * Carregando imagens no ACR

Avance para o próximo tutorial para saber mais sobre a implantação de um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
