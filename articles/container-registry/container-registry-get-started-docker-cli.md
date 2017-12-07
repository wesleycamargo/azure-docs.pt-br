---
title: Enviar por push a imagem do Docker para o registro privado do Azure
description: "Envie e obtenha imagens do Docker para um registro de contêiner privado no Azure usando a CLI do Docker"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21d1abfbb49eaeae654a600d35ab350b96a12fd3
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Envie sua primeira imagem para um registro de contêiner privado do Docker usando a CLI do Docker

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do [Docker](http://hub.docker.com), de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens públicas do Docker. Você pode usar a [Interface de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para [logon](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/) e outras operações no registro de contêiner.

Nas etapas a seguir, você baixará uma [imagem Nginx](https://store.docker.com/images/nginx) oficial do registro do Hub do Docker público, a rotulará para seu registro de contêiner do Azure privado, enviará por push para o registro e, em seguida, efetuará pull dela do registro.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de Contêiner do Azure** - crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI do Docker** – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Fazer logon em um registro

Há [várias maneiras de autenticar](container-registry-authentication.md) no registro de contêiner privado. É o método recomendado ao trabalhar em uma linha de comando com o comando [logon de acr az](/cli/azure/acr?view=azure-cli-latest#az_acr_login) da CLI do Azure. Por exemplo, para fazer logon em um registro chamado *meuregistro*:

```azurecli
az acr login --name myregistry
```

Você também pode fazer logon com o [logon do docker](https://docs.docker.com/engine/reference/commandline/login/). O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, você pode [atribuir uma entidade de serviço](container-registry-authentication.md#service-principal) ao registro para um cenário de automação.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Os dois comandos retornam `Login Succeeded` após a conclusão. Se você usar o `docker login`, talvez veja também um aviso de segurança recomendando o uso do parâmetro `--password-stdin`. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Para obter mais informações, consulte a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/).

> [!TIP]
> Sempre especifique o nome totalmente qualificado do registro (todas as letras minúsculas) quando você usar `docker login` e quando marcar imagens para envio por push para o registro. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Fazer pull da imagem oficial do Nginx

Primeiro, extraia a imagem Nginx pública para seu computador local.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Execute o seguinte comando [docker run](https://docs.docker.com/engine/reference/run/) para iniciar uma instância local do contêiner Nginx interativamente (`-it`) na porta 8080. O argumento `--rm` especifica que o contêiner deverá ser removido quando você o parar.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Navegue até [http://localhost:8080](http://localhost:8080) para exibir a página da Web padrão servida pelo Nginx no contêiner em execução. Você verá uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como você iniciou o contêiner interativamente com `-it`, poderá ver a saída do servidor Nginx na linha de comando depois de ir até ele em seu navegador.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Use [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho totalmente qualificado para o registro. Este exemplo especifica o namespace `samples` para evitar confusão na raiz do registro.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para saber mais sobre a marcação com namespaces, veja a seção [Namespaces de repositório](container-registry-best-practices.md#repository-namespaces) de [Práticas recomendadas para o Registro de Contêiner do Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar por push a imagem ao registro

Agora que você já marcou a imagem com o caminho totalmente qualificado para o registro privado, poderá enviá-la por push para o registro com [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Fazer pull da imagem do registro

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para fazer pull da imagem do registro:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contêiner Nginx

Use o comando [docker run](https://docs.docker.com/engine/reference/run/) para executar a imagem extraída por pull do registro:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue até [http://localhost:8080/](http://localhost:8080) para exibir o contêiner em execução.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="remove-the-image-optional"></a>Remover a imagem (opcional)

Se você não precisar mais da imagem Nginx, poderá excluí-la localmente com o comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover as imagens de seu registro de contêiner do Azure, você pode usar o comando [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete) da CLI do Azure. Por exemplo, o comando a seguir exclui o manifesto referenciado por uma marca, quaisquer dados da camada associada e todas as outras marcas que façam referência ao manifesto.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Próximas etapas

Agora que conhece os fundamentos, você está pronto para começar a usar o registro! Por exemplo, implante imagens de contêiner do registro em um cluster de [Serviço de Contêiner do Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md).
