---
title: "Entidade de serviço para cluster Kubernetes do Azure"
description: "Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster Kubernetes no AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a7c80b64a33f4f71c694f80bf3e68f39ecd01828
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="service-principals-with-azure-container-service-aks"></a>Entidades de serviço com o Serviço de Contêiner do Azure (AKS)

Um cluster AKS requer uma [entidade de serviço do Azure Active Directory][aad-service-principal] para interagir com as APIs do Azure. A entidade de serviço é necessária para gerenciar dinamicamente recursos como [rotas definidas pelo usuário][user-defined-routes] e o [Azure Load Balancer da Camada 4][azure-load-balancer-overview].

Este artigo mostra diferentes opções para configurar uma entidade de serviço para o cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar


Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure Ad ou assinatura que atribua as permissões necessárias ou pré-crie uma entidade de serviço para o cluster Kubernetes.

Você também precisa da CLI do Azure versão 2.0.27 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Criar SP com cluster AKS

Ao implantar um cluster AKS com o comando `az aks create`, você tem a opção para gerar automaticamente uma entidade de serviço.

No exemplo a seguir, um cluster AKS é criado e, como não há uma entidade de serviço existente especificada, será criada uma entidade de serviço para o cluster. Para concluir esta operação, sua conta deverá ter os direitos adequados para a criação de uma entidade de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Usa um SP existente

Uma entidade de serviço existente do Azure AD pode ser usada ou criada previamente para uso com um cluster AKS. Isso será útil ao implantar um formulário de cluster no portal do Azure onde for necessário fornecer as informações sobre a entidade de serviço. Ao usar uma entidade de serviço existente, o segredo do cliente deverá ser configurado como uma senha.

## <a name="pre-create-a-new-sp"></a>Pré-criar um novo SP

Para criar a entidade de serviço com a CLI do Azure, use o comando [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante à seguinte. Anote `appId` e `password`. Esses valores são usados durante a criação de um cluster AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Usa um SP existente

Ao usar uma entidade de serviço previamente criada, forneça `appId` e `password` como valores de argumento para o comando `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Se você estiver implantando um cluster do Serviço de Contêiner do Azure usando o portal do Azure, insira o valor `appId` no campo **ID do cliente de entidade de serviço** e o valor `password` no campo **Segredo do cliente de entidade de serviço** no formulário de configuração de cluster do AKS.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considerações adicionais

Ao trabalhar com entidades de serviço AKS e do Azure AD, tenha em mente o seguinte.

* A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
* Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço para um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure AD (por exemplo: `https://www.contoso.org/example`). A URL para o aplicativo não precisa ser um ponto de extremidade real.
* Ao especificar a **ID do cliente** da entidade de serviço, você pode usar o valor de `appId` (conforme mostrado neste artigo) ou `name` da entidade de serviço correspondente (por exemplo, `https://www.contoso.org/example`).
* Nas VMs mestre e de nó no cluster Kubernetes, as credenciais de entidade de serviço são armazenadas no arquivo `/etc/kubernetes/azure.json`.
* Se você usar o comando `az aks create` para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo `~/.azure/acsServicePrincipal.json` no computador usado para executar o comando.
* Ao excluir um cluster AKS que foi criado por `az aks create`, a entidade de serviço que foi criada automaticamente não será excluída. Você pode usar `az ad sp delete --id $clientID` para excluí-la.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre entidades de serviço do Azure Active Directory, veja a documentação de aplicativos do Azure AD.

> [!div class="nextstepaction"]
> [Objetos de aplicativo e entidade de serviço][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md