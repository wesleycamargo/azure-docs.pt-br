---
title: "Tutorial de Instâncias de Contêiner do Azure – implantar aplicativo"
description: "Tutorial de Instâncias de Contêiner do Azure parte 3 de 3 – implantar aplicativo"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 250f74b1a05959b93000452c4d5f025311f379d8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Implantar um contêiner em Instâncias de Contêiner do Azure

Este é o tutorial final de uma série de três partes. Anteriormente na série, [uma imagem de contêiner foi criada](container-instances-tutorial-prepare-app.md) e [enviada por push a um Registro de Contêiner do Azure](container-instances-tutorial-prepare-acr.md). Este artigo conclui a série de tutoriais implantando o contêiner em Instâncias de Contêiner do Azure.

Neste tutorial, você:

> [!div class="checklist"]
> * Vai aprender como implantar o contêiner do Registro de Contêiner do Azure usando a CLI do Azure
> * Vai aprender como ver o aplicativo no navegador
> * Vai aprender como ver os logs de contêiner

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer a execução da CLI do Azure versão 2.0.23 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure][azure-cli-install].

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Você precisa instalar o ambiente de desenvolvimento do Docker e a CLI do Azure no computador local para concluir este tutorial.

## <a name="deploy-the-container-using-the-azure-cli"></a>Implantar o contêiner usando a CLI do Azure

A CLI do Azure permite a implantação de um contêiner nas Instâncias de Contêiner do Azure por meio de um único comando. Como a imagem de contêiner é hospedada no Registro de Contêiner do Azure privado, você deve incluir as credenciais necessárias para acessá-la. Obtenha as credenciais com os seguintes comandos de CLI do Azure.

Servidor de logon do registro de contêiner (atualize com seu nome de registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Senha de registro de contêiner:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implantar a imagem de contêiner do registro de contêiner com uma solicitação de recurso de 1 núcleo de CPU e 1 GB de memória, execute o seguinte comando. Substitua `<acrLoginServer>` e `<acrPassword>` pelos valores obtidos dos dois comandos anteriores. Substitua `<acrName>` pelo nome do seu Registro de contêiner.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure Resource Manager. O valor `--dns-name-label` deve ser exclusivo dentro da região do Azure em que você criar a instância do contêiner. Atualize o valor no exemplo anterior se você receber uma mensagem de erro do **rótulo do nome DNS** ao executar o comando.

Para exibir o estado da implantação, use o comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Repita o comando [az container show][az-container-show] até que o estado mude de *Pendente* para *Executando*, o que deve levar menos de um minuto. Quando o contêiner estiver *Executando*, vá para a próxima etapa.

## <a name="view-the-application-and-container-logs"></a>Exibir os logs do aplicativo e do contêiner

Depois que a implantação for bem-sucedida, exiba o nome de domínio totalmente qualificado (FQDN) do contêiner com o comando [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Saída de exemplo: `"aci-demo.eastus.azurecontainer.io"`

Para ver o aplicativo em execução, navegue até o nome DNS exibido em seu navegador favorito:

![Aplicativo Olá, Mundo no navegador][aci-app-browser]

Você também pode exibir a saída do log do contêiner:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Saída:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar mais de nenhum dos recursos criados nessa série de tutoriais, você poderá executar o comando [az group delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Esse comando exclui o Registro de contêiner que você criou, bem como o contêiner em execução e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu o processo de implantação dos seus contêineres nas Instâncias de Contêiner do Azure. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantar o contêiner do Registro de Contêiner do Azure usando a CLI do Azure
> * Exibir o aplicativo no navegador
> * Exibir os logs de contêiner

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
