---
title: Gerenciar o cluster Kubernetes do Azure com a interface do usuário da Web
description: Saiba como usar o painel de interface do usuário web Kubernetes interno com o serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307918"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Acesse o painel do Kubernetes com o AKS (Serviço de Kubernetes do Azure)

Kubernetes inclui um painel da web que pode ser usado para operações básicas de gerenciamento. Este artigo mostra como acessar o painel do Kubernetes usando a CLI do Azure e, em seguida, orienta você por meio de algumas operações básicas do painel. Para obter mais informações sobre o painel do Kubernetes, consulte [Painel de interface do usuário Web do Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster do AKS e estabelecido uma conexão `kubectl` com o cluster. Se você precisar criar um cluster AKS, consulte o [Início rápido do AKS][aks-quickstart].

Você também precisa da CLI do Azure versão 2.0.27 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Iniciar o painel do Kubernetes

Para iniciar o painel de Kubernetes, use o [comando de navegação][az-aks-browse] az aks. O exemplo abaixo abre o painel para o cluster nomeado *myAKSCluster* em um grupo de recursos chamado *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API do Kubernetes, e abre um navegador da Web para o painel do Kubernetes.

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados para RBAC

Se o cluster do AKS usa RBAC, um *ClusterRoleBinding* deve ser criado antes de poder acessar o painel corretamente. Por padrão, o painel do Kubernetes é implantado com um mínimo de acesso de leitura e exibe os erros de acesso do RBAC. O painel do Kubernetes não oferece suporte a credenciais fornecidas pelo usuário para determinar o nível de acesso, em vez disso, ele usa as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder acesso adicional para o *painel do kubernetes* conta de serviço, no entanto, isso pode ser um vetor para elevação de privilégios. Você também pode integrar a autenticação do Active Directory para fornecer um nível mais granular de acesso.

Para criar uma associação, use o comando [kubectl criar clusterrolebinding] [ kubectl-create-clusterrolebinding] conforme mostrado no exemplo a seguir. 

> [!WARNING]
> Essa associação de exemplo não se aplica a todos os componentes de autenticação adicional e pode levar a uso inseguro. O painel do Kubernetes está aberto para qualquer pessoa com acesso à URL. Não expor publicamente o painel do Kubernetes.
>
> Para obter mais informações sobre como usar os métodos de autenticação diferentes, consulte o wiki do painel do Kubernetes em [controles de acesso][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Agora você pode acessar o painel do Kubernetes no seu cluster habilitado pelo RBAC. Para iniciar o painel de Kubernetes, use o comando [aks browse][az-aks-browse] para iniciar o painel do Kubernetes.

## <a name="run-an-application"></a>Executar um aplicativo

No painel do Kubernetes, clique no botão **Criar** na janela superior direita. Dê o nome `nginx` para a implantação e insira `nginx:latest` para o nome da imagem de contêiner. Em **Serviço**, selecione **Externo** e insira `80` para a porta e a porta de destino.

Quando estiver pronto, clique em **Implantar** para criar a implantação.

![Caixa de diálogo Criar serviço Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Exibir o aplicativo

É possível ver o status do aplicativo no painel do Kubernetes. Quando o aplicativo estiver em execução, cada componente tem uma caixa de seleção verde ao lado.

![Pods Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Para obter mais informações sobre os pods de aplicativo, clique em **Pods** no menu esquerdo e selecione o pod **NGINX**. Nele você poderá ver informações específicas do pod, como o consumo de recursos.

![Recursos do Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Para encontrar o endereço IP do aplicativo, clique em **Serviços** no menu esquerdo e selecione o serviço **NGINX**.

![nginx view](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Editar o aplicativo

Além de criar e exibir os aplicativos, o painel do Kubernetes pode ser usado para editar e atualizar implantações de aplicativo.

Para editar uma implantação, clique em **Implantações** no menu esquerdo e selecione a implantação **NGINX**. Por fim, selecione **Editar** na barra de navegação superior à direita.

![Editar Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Localize o valor `spec.replica`, que deve ser 1 e altere-o para 3. Dessa forma, a contagem de réplica da implantação NGINX é aumentada de 1 para 3.

Selecione **Atualizar** quando estiver pronto.

![Editar Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o painel do Kubernetes, consulte a documentação do Kubernetes.

> [!div class="nextstepaction"]
> [Painel de interface do usuário Web do Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
