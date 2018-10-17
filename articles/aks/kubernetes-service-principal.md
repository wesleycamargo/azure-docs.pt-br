---
title: Entidades de serviço para o AKS (Serviço de Kubernetes do Azure)
description: Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394583"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entidades de serviço com o AKS (Serviço de Kubernetes do Azure)

Para interagir com as APIs do Azure, um cluster do AKS requer uma [entidade de serviço do Azure AD (Active Directory)][aad-service-principal]. A entidade de serviço é necessária para criar e gerenciar outros recursos do Azure, como o Azure Load Balancer ou o ACR (Registro de Contêiner do Azure).

Este artigo mostra como criar e usar uma entidade de serviço para seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure AD ou da assinatura que atribua as permissões necessárias ou crie previamente uma entidade de serviço para uso com o cluster do AKS.

Você também precisa ter instalada e configurada a CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e usar uma entidade de serviço automaticamente

Quando você cria um cluster do AKS no portal do Azure ou usando o comando [criar az aks] [ az-aks-create], o Azure pode gerar uma entidade de serviço automaticamente.

No exemplo a seguir da CLI do Azure, uma entidade de serviço não foi especificada. Neste cenário, a CLI do Azure cria um entidade de serviço para o cluster do AKS. Para concluir a operação com êxito, sua conta do Azure deve ter os direitos necessários para criar uma entidade de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Criar uma entidade de serviço manualmente

Para criar uma entidade de serviço com a CLI do Azure manualmente, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados quando você criar um cluster do AKS na próxima seção.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Especificar uma entidade de serviço para um cluster do AKS

Para usar uma entidade de serviço existente quando você criar um cluster do AKS usando o comando [criar az aks][az-aks-create], use os parâmetros `--service-principal` e `--client-secret` para especificar o `appId` e a `password` da saída do comando [az ad sp create-for-rbac] [ az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Se você implantar um cluster do AKS usando o portal do Azure, na página *Autenticação* do diálogo **Criar cluster do Kubernetes**, escolha **Configurar entidade de serviço**. Selecione **Usar existente** e especifique os seguintes valores:

- **ID do cliente da entidade de serviço** é seu *appId*
- **Segredo do cliente da entidade de serviço** é o valor da *senha*

![Imagem de navegação para o Voto do Azure](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>Considerações adicionais

Ao usar o AKS e as entidades de serviço do Azure AD, tenha em mente as considerações a seguir.

- A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
- Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço de um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure AD (por exemplo: *https://www.contoso.org/example*). A URL para o aplicativo não precisa ser um ponto de extremidade real.
- Ao especificar a **ID do cliente** da entidade de serviço, use o valor de `appId`.
- Nas VMs mestre e de nó no cluster Kubernetes, as credenciais da entidade de serviço são armazenadas no arquivo `/etc/kubernetes/azure.json`
- Se você usar o comando [az aks create][az-aks-create] para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo `~/.azure/aksServicePrincipal.json` no computador usado para executar o comando.
- Ao excluir um cluster do AKS que foi criado por [az aks create][az-aks-create], a entidade de serviço que foi criada automaticamente não será excluída.
    - Para excluir a entidade de serviço, primeiro obtenha a ID para o serviço principal com [az ad app list][az-ad-app-list]. O exemplo a seguir consulta o cluster chamado *myAKSCluster* e, em seguida, exclui a ID do aplicativo com [az ad app delete][az-ad-app-delete]. Substitua esses nomes pelos seus próprios valores:

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre entidades de serviço do Azure Active Directory, consulte [Objetos de aplicativo e de entidade de serviço][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
