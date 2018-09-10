---
title: Tutorial do Kubernetes no Azure - Implantar um cluster
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você cria um cluster AKS e usa o kubectl para conectar-se ao nó mestre do Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 80b011f9df389098095f58c02008da891b2aa8a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41917520"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: implantar um cluster do Serviço de Kubernetes do Azure (AKS)

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o AKS, você pode provisionar rapidamente um cluster Kubernetes pronto para produção. Neste tutorial, a terceira parte de sete, um cluster Kubernetes é implantado no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma entidade de serviço para interações de recursos
> * Implantar um cluster AKS Kubernetes
> * Instalar a CLI Kubernetes (kubectl)
> * Configurar o kubectil para conectar-se ao cluster AKS

Nos tutoriais subsequentes, o aplicativo Azure Vote é implantado no cluster, dimensionado e atualizado.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige que você esteja executando a CLI do Azure versão 2.0.44 ou posterior. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para permitir a interação de um cluster AKS com outros recursos do Azure, usamos uma entidade de serviço do Azure Active Directory. Essa entidade de serviço pode ser criada automaticamente pelo portal ou pela CLI do Azure, ou você pode criar previamente um e atribuir permissões adicionais. Neste tutorial, você cria uma entidade de serviço, concede acesso à instância do ACR (Registro de Contêiner do Azure) criada no tutorial anterior e, em seguida, cria um cluster AKS.

Use o comando [az ad sp create-for-rbac][] para criar uma entidade de serviço. O parâmetro `--skip-assignment` limita a atribuição de outras permissões.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote a *appId* e a *senha*. Esses valores serão usados nas próximas etapas.

## <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

Para acessar as imagens armazenadas no ACR, conceda à entidade de serviço AKS os direitos corretos para efetuar pull de imagens do ACR.

Primeiro, obtenha a ID de recurso do ACR usando [az acr show][]. Atualize o nome de registro `<acrName>` da sua instância do ACR e o grupo de recursos no qual a instância do ACR está localizada.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Para conceder o acesso correto para que o cluster do AKS use as imagens armazenadas no ACR, crie uma atribuição de função com o comando [az role assignment create][]. Substitua `<appId`> e `<acrId>` pelos valores coletados nas duas etapas anteriores.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role Reader
```

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster Kubernetes

Os cluster AKS podem usar os controles de acesso baseado em função (RBAC) do Kubernetes. Esses controles permitem que você defina o acesso a recursos com base em funções atribuídas aos usuários. Permissões podem ser combinadas, se um usuário for atribuído a várias funções e permissões podem ser definidas para um único namespace ou para todo o cluster. O RBAC do Kubernetes está atualmente em versão prévia para clusters AKS. Por padrão, a CLI do Azure habilita o RBAC automaticamente quando você cria um cluster AKS.

Crie um cluster AKS usando [az aks create][]. O exemplo abaixo cria um cluster chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. Este grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr]. Forneça seus próprios `<appId>` e `<password>` da etapa anterior na qual a entidade de serviço foi criada.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Após alguns minutos, a implantação é concluída e retorna as informações formatadas em JSON sobre a implantação do AKS.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI Kubernetes

Para se conectar ao cluster Kubernetes do computador local, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes.

Se você usa o Azure Cloud Shell, o `kubectl` já estará instalado. Se você quiser instalá-lo localmente, use o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Conectar-se ao cluster usando o kubectl

Para configurar o `kubectl` a fim de conectá-lo ao seu cluster Kubernetes, use o comando [az aks get-credentials][]. O exemplo a seguir obtém as credenciais para o nome do cluster AKS *myAKSCluster* em *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, execute o comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.9
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes foi implantado no AKS e você configurou o `kubectl` para conectar-se a ele. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma entidade de serviço para interações de recursos
> * Implantar um cluster AKS Kubernetes
> * Instalar a CLI Kubernetes (kubectl)
> * Configurar o kubectil para conectar-se ao cluster AKS

Siga para o próximo tutorial para saber como implantar um aplicativo ao cluster.

> [!div class="nextstepaction"]
> [Implantar o aplicativo no Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
