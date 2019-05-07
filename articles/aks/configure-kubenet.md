---
title: Configurar a rede kubenet no Serviço de Kubernetes do Azure (AKS)
description: Saiba como configurar a rede kubenet (básica) no Serviço de Kubernetes do Azure (AKS) para implantar um cluster do AKS em uma rede virtual e sub-rede existentes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 4d2ab19fafc265d70028d5ee192efc60a5a8eaff
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073984"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Use a rede do kubenet com seus próprios intervalos de endereços IP no Serviço de Kubernetes do Azure (AKS)

Por padrão, os clusters do AKS usam o [kubenet][kubenet], e uma rede virtual e uma sub-rede do Azure são criadas para você. Com o *kubenet*, os nós obtêm um endereço IP de uma sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente da sub-rede da rede virtual do Azure dos nós. A NAT (conversão de endereços de rede), então, é configurada para que os pods possam acessar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é configurado via NAT como o endereço IP primário do nó. Essa abordagem reduz muito o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Com a [CNI (Interface de Rede de Contêiner) do Azure][cni-networking], cada pod obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o seu espaço de rede e devem ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem exige mais planejamento e, muitas vezes, leva à exaustão do endereço IP ou à necessidade de recriar os clusters em uma sub-rede maior conforme as demandas de aplicativo aumentam.

Este artigo mostra como usar a rede *kubenet* para criar e usar uma sub-rede da rede virtual de um cluster do AKS. Para saber mais sobre opções de rede e considerações, confira [Conceitos de rede para o Kubernetes e o AKS][aks-network-concepts].

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.56 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Visão geral da rede do kubenet com sua própria sub-rede

Em muitos ambientes, você definiu redes virtuais e sub-redes com intervalos de endereços IP alocados. Esses recursos de rede virtual são usados para dar suporte a vários serviços e aplicativos. Para fornecer conectividade de rede, os clusters do AKS podem usar o *kubenet* (rede básica) ou o CNI do Azure (*rede avançada*).

Com o *kubenet*, somente os nós recebem um endereço IP na sub-rede da rede virtual. Os pods não podem se comunicar diretamente entre si. Em vez disso, o roteamento definido pelo usuário (UDR) e o encaminhamento de IP são usados para conectividade entre os pods nos nós. Você também pode implantar pods atrás de um serviço que recebe um endereço IP atribuído e o tráfego de balanceamento de carga do aplicativo. O diagrama a seguir mostra como os nós do AKS recebem um endereço IP na sub-rede da rede virtual, mas não nos pods:

![Modelo de rede Kubenet com um cluster do AKS](media/use-kubenet/kubenet-overview.png)

O Azure dá suporte a um máximo de 400 rotas em um UDR, portanto, você não pode ter um cluster do AKS com mais de 400 nós. Os recursos do AKS, como [Nós Virtuais][virtual-nodes], ou as políticas de rede não são compatíveis com *kubenet*.

Com o *CNI do Azure*, cada pod recebe um endereço IP na sub-rede IP e pode se comunicar diretamente com outros pods e serviços. Seus clusters podem ser tão grandes quanto o intervalo de endereços IP especificado. No entanto, o intervalo de endereços IP deve ser planejado com antecedência e todos os endereços IP são consumidos pelos nós AKS com base no número máximo de pods a que eles podem dar suporte. Os recursos e cenários de rede avançados, como [Nós Virtuais][virtual-nodes] ou políticas de rede, são compatíveis com o *CNI do Azure*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade e esgotamento de endereços IP

Com o *CNI do Azure*, um problema comum é que o intervalo de endereços IP atribuído é muito pequeno para então adicionar nós adicionais ao dimensionar ou atualizar um cluster. A equipe de rede também pode não conseguir emitir um intervalo de endereços IP grande o suficiente para dar suporte às demandas de aplicativos esperadas.

Como um compromisso, você pode criar um cluster do AKS que usa o *kubenet* e se conectar a uma sub-rede de rede virtual existente. Essa abordagem permite que os nós recebam endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP antecipadamente para todos os possíveis pods que podem ser executados no cluster.

Com o *kubenet*, você pode usar um intervalo de endereços IP muito menor e ser capaz de oferecer suporte a grandes clusters e demandas de aplicativos. Por exemplo, mesmo com um intervalo de endereços IP */27*, você pode executar um cluster de 20 a 25 nós com espaço suficiente para dimensionar ou atualizar. Esse tamanho de cluster daria suporte a até *2.200-2.750* pods (com um máximo padrão de 110 pods por nó).

Os seguintes cálculos básicos comparam a diferença nos modelos de rede:

- **kubenet** – um simples intervalo de endereços IP de */24* pode dar suporte a até *251* nós no cluster (cada sub-rede de rede virtual do Azure reserva os três primeiros endereços IP para operações de gerenciamento)
  - Esta contagem de nós pode dar suporte a até *27.610* pods (com um máximo padrão de 110 pods por nó com *kubenet*)
- **CNI do Azure** – esse mesmo intervalo de sub-rede */24* básica só podia dar suporte a um máximo de *8* nós no cluster
  - Esta contagem de nós pode dar suporte a até *240* pods (com um máximo padrão de 30 pods por nó com o *CNI do Azure*)

> [!NOTE]
> Esses máximos não levam em conta operações de atualização ou dimensionamento. Na prática, não é possível executar o número máximo de nós a que o intervalo de endereços IP de sub-rede dá suporte. É preciso deixar alguns endereços IP disponíveis para uso durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Emparelhamento de rede virtual e conexões do ExpressRoute

Para fornecer conectividade local, as abordagens de rede *kubenet* e *CNI do Azure* podem usar o [emparelhamento de rede virtual do Azure][vnet-peering] ou as [conexões do ExpressRoute][express-route]. Planeje seus intervalos de endereços IP com cuidado para evitar sobreposição e roteamento de tráfego incorreto. Por exemplo, muitas redes locais usam um intervalo de endereços *10.0.0.0/8* que é anunciado pela conexão ExpressRoute. É recomendável criar seus clusters do AKS nas sub-redes da rede virtual do Azure fora desse intervalo de endereços, como *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolher um modelo de rede para usar

A escolha de qual plug-in de rede usar para seu cluster do AKS geralmente é um equilíbrio entre a flexibilidade e as necessidades de configuração avançada. As seguintes considerações ajudam a delinear quando cada modelo de rede pode ser o mais adequado.

Use o *kubenet* quando:

- Você tiver espaço de endereço IP limitado.
- A maior parte da comunicação do pod estiver dentro do cluster.
- Você não precisar de recursos avançados, como nós virtuais ou políticas de rede.

Use o *CNI do Azure* quando:

- Você tem espaço de endereço IP disponível.
- A maior parte da comunicação do pod destina-se a recursos fora do cluster.
- Você não quer gerenciar as UDRs.
- Você precisa de recursos avançados, como nós virtuais ou políticas de rede.

> [!NOTE]
> Kuberouter torna possível habilitar a política de rede ao usar kubenet e pode ser instalado como um daemonset em um cluster AKS. Esteja ciente de roteador kube ainda está em beta e não há suporte é oferecido pela Microsoft para o projeto.

## <a name="create-a-virtual-network-and-subnet"></a>Criar a rede virtual e a sub-rede

Para começar a usar a *kubenet* e sua própria sub-rede de rede virtual, primeiro crie um grupo de recursos usando o comando [az group create][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se você não tiver uma rede virtual e uma sub-rede para usar, crie esses recursos de rede usando o comando [az network vnet create][az-network-vnet-create]. No exemplo a seguir, a rede virtual é denominada *myVnet* com o prefixo de endereço *10.0.0.0/8*. Uma sub-rede é criada com o nome *myAKSSubnet* com o prefixo de endereço *10.240.0.0/16*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar uma entidade de serviço e atribuir permissões

Para permitir a interação de um cluster AKS com outros recursos do Azure, usamos uma entidade de serviço do Azure Active Directory. A entidade de serviço precisa ter permissões para gerenciar a rede virtual e a sub-rede que usam os nós do AKS. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída de exemplo a seguir mostra a ID do aplicativo e a senha da entidade de serviço. Esses valores são usados em etapas adicionais para atribuir uma função à entidade de serviço e, em seguida, criar o cluster do AKS:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Para atribuir as delegações corretas nas etapas restantes, use os comandos [az network vnet show][az-network-vnet-show] e [az network vnet subnet show][az-network-vnet-subnet-show] para obter as IDs de recursos necessários. Essas IDs de recursos são armazenadas como variáveis e referenciadas nas etapas restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora, atribua a entidade de serviço para suas permissões de *Colaborador* do cluster do AKS na rede virtual usando o comando [criação de função de atribuição az][az-role-assignment-create]. Forneça suas próprias  *\<appId >* conforme mostrado na saída do comando anterior para criar a entidade de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster do AKS na rede virtual

Agora você criou uma rede virtual e uma sub-rede, e criou e atribuiu permissões para que uma entidade de serviço use esses recursos de rede. Agora crie um cluster do AKS na sua rede virtual e sub-rede usando o comando [az aks create][az-aks-create]. Definir sua própria entidade de serviço  *\<appId >* e  *\<senha >*, conforme mostrado na saída do comando anterior para criar a entidade de serviço.

Os seguintes intervalos de endereços IP também são definidos como parte do processo de criação de cluster:

* O *--service-cidr* é usado para designar um endereço IP aos serviços internos no cluster do AKS. Esse intervalo de endereços IP deve ser um espaço de endereço que não esteja em uso em outro lugar no ambiente de rede. Isso inclui qualquer intervalo de rede local se você conectar ou planeja conectar suas redes virtuais do Azure usando o ExpressRoute ou uma conexão VPN Site a Site.

* O endereço *--dns-service-ip* deve ser o endereço *.10* do seu intervalo de endereço IP de serviço.

* *--pod-cidr* deve ser um espaço de endereço grande que não esteja em uso em outro lugar no ambiente de rede. Isso inclui qualquer intervalo de rede local se você conectar ou planeja conectar suas redes virtuais do Azure usando o ExpressRoute ou uma conexão VPN Site a Site.
    * Esse intervalo de endereços deve ser grande o suficiente para acomodar o número de nós que você espera ampliar. Se precisar de mais endereços para nós adicionais, não é possível alterar esse intervalo de endereços depois que o cluster for implantado.
    * O intervalo de endereços IP do pod é usado para atribuir um espaço de endereço */24* a cada nó no cluster. No exemplo a seguir, o *--pod-cidr* do *192.168.0.0/16* atribui o primeiro nó *192.168.0.0/24*, o segundo nó *192.168.1.0/24* e o terceiro nó *192.168.2.0/24*.
    * À medida que o cluster é dimensionado ou atualizado, a plataforma do Azure continua atribuindo um intervalo de endereços IP do pod a cada novo nó.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Quando você cria um cluster do AKS, um grupo de segurança de rede e uma tabela de rotas são criados. Esses recursos de rede são gerenciados pelo painel de controle do AKS. O grupo de segurança de rede é associado automaticamente a NICs virtuais em seus nós. A tabela de rotas é associada automaticamente a sub-rede da rede virtual. Regras de grupo de segurança de rede e as tabelas de rotas e são atualizados automaticamente ao criar e expor serviços.

## <a name="next-steps"></a>Próximas etapas

Com um cluster do AKS implantado em sua sub-rede de rede virtual existente, agora você pode usar o cluster como normal. Comece a [criar aplicativos usando o Azure Dev Spaces][dev-spaces] ou [usando o Draft][use-draft], ou [implante aplicativos usando o Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
