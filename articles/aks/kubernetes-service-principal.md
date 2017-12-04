---
title: "Entidade de serviço para cluster Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster Kubernetes no AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 359887a8527d5432e705d9739e30f0eb2363e34f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Entidades de serviço com o Serviço de Contêiner do Azure (AKS)

Um cluster AKS requer uma [entidade de serviço do Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) para interagir com as APIs do Azure. A entidade de serviço é necessária para gerenciar dinamicamente recursos como [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) e o [Azure Load Balancer da Camada 4](../load-balancer/load-balancer-overview.md).

Este artigo mostra diferentes opções para configurar uma entidade de serviço para o cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, veja o [Início rápido do AKS](./kubernetes-walkthrough.md).

Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure Ad ou assinatura que atribua as permissões necessárias ou pré-crie uma entidade de serviço para o cluster Kubernetes.

Você também precisa da CLI do Azure versão 2.0.21 ou posterior instalada e configurada. Execute az --version para localizar a versão. Se você precisa instalar ou fazer upgrade, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Criar SP com cluster AKS

Ao implantar um cluster AKS com o comando `az aks create`, você tem a opção para gerar automaticamente uma entidade de serviço.

No exemplo a seguir, um cluster AKS é criado e, como não há uma entidade de serviço existente especificada, será criada uma entidade de serviço para o cluster. Para concluir esta operação, sua conta deverá ter os direitos adequados para a criação de uma entidade de serviço.

```azurecli
az aks create --name myK8SCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Usa um SP existente

Uma entidade de serviço existente do Azure AD pode ser usada ou criada previamente para uso com um cluster AKS. Isso será útil ao implantar um formulário de cluster no portal do Azure onde for necessário fornecer as informações sobre a entidade de serviço.

Ao usar uma entidade de serviço existente, ela deverá atender aos seguintes requisitos:

- Segredo do cliente: deve ser uma senha

## <a name="pre-create-a-new-sp"></a>Pré-criar um novo SP

Para criar a entidade de serviço com a CLI do Azure, use o comando [az ad sp create-for-rbac]().

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante à seguinte. Anote `appId` e `password`. Esses valores são usados durante a criação de um cluster AKS.

```
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
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> --client-secret <password>
```

Se você estiver implantando um cluster AKS do portal do Azure, insira esses valores no formulário de configuração de cluster AKS.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considerações adicionais

Ao trabalhar com entidades de serviço AKS e do Azure AD, tenha em mente o seguinte.

* A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
* Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço para um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure AD (por exemplo: `https://www.contoso.org/example`). A URL para o aplicativo não precisa ser um ponto de extremidade real.
* Ao especificar a **ID do cliente** da entidade de serviço, você pode usar o valor de `appId` (conforme mostrado neste artigo) ou `name` da entidade de serviço correspondente (por exemplo, `https://www.contoso.org/example`).
* Nas VMs mestre e de nó no cluster Kubernetes, as credenciais de entidade de serviço são armazenadas no arquivo /etc/kubernetes/azure.json.
* Se você usar o comando `az aks create` para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo ~/.azure/acsServicePrincipal.json no computador usado para executar o comando.
* Quando você usa o comando `az aks create` para gerar a entidade de serviço automaticamente, ela também pode autenticar com um [registro de contêiner do Azure](../container-registry/container-registry-intro.md) criado na mesma assinatura.
* Ao excluir um cluster AKS que foi criado por `az aks create`, a entidade de serviço que foi criada automaticamente não será excluída. Você pode usar `az ad sp delete --id $clientID` para excluí-la.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre entidades de serviço do Azure Active Directory, veja a documentação de aplicativos do Azure AD.

> [!div class="nextstepaction"]
> [Objetos de aplicativo e entidade de serviço](../active-directory/develop/active-directory-application-objects.md)
