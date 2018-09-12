---
title: Configuração de rede no Serviço do Kubernetes do Azure (AKS)
description: Saiba mais sobre a configuração de rede básica e avançada no Serviço de Kubernetes do Azure (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/31/2018
ms.author: marsma
ms.openlocfilehash: e78be76d68cf75cf9d59f5b5dff86c65524275a9
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697223"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Configuração de rede no Serviço do Kubernetes do Azure (AKS)

Quando você cria um cluster Serviço do Kubernetes do Azure (AKS), você pode selecionar entre duas opções de rede: **Básica** ou **Avançada**.

## <a name="basic-networking"></a>Rede básica

A opção de rede **básica** é a configuração padrão para a criação de cluster AKS. A configuração de rede do cluster e seus pods é gerenciada completamente pelo Azure e é apropriada para implantações que não exigem configuração personalizada de VNet. Você não tem controle sobre a configuração de rede, como sub-redes ou intervalos de endereço IP atribuídos ao cluster quando você seleciona a rede básica.

Os nós em um cluster AKS configurado para o uso básico de rede o plug-in do Kubernetes [kubenet][kubenet].

## <a name="advanced-networking"></a>Rede avançada

A rede **avançada** coloca seus pods em uma rede virtual do Azure (VNet) que você configura, fornecendo a eles conectividade automática para recursos de rede virtual e integração com o sofisticado conjunto de recursos que as VNets oferecem. A rede avançada fica disponível ao implantar clusters AKS com o [portal do Azure][portal], CLI do Azure ou com um modelo do Resource Manager.

Os nós em um cluster AKS configurado para rede avançada usa o plug-in do Kubernetes [Interface de Rede de Contêiner do Azure (CNI)][cni-networking].

![Diagrama que mostra dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Recursos de rede avançada

A rede avançada proporciona os seguintes benefícios:

* Implantar o cluster AKS em uma rede virtual existente ou criar uma nova rede virtual e sub-rede para o cluster.
* Cada pod do cluster é atribuído a um endereço IP na rede virtual e pode se comunicar diretamente com outros pods no cluster e outros nós na VNet.
* Um pod pode se conectar com outros serviços em uma VNet pareada e com redes locais, em conexões VPN site a site (S2S) e do ExpressRoute. Os pods também são acessíveis do local.
* Expor um serviço do Kubernetes externa ou internamente por meio do Azure Load Balancer. Também é um recurso de rede básica.
* Os pods em uma sub-rede que têm pontos de extremidade de serviço habilitados podem se conectar com segurança aos serviços do Azure, por exemplo o Azure Storage e o banco de dados SQL.
* Usar rotas definidas pelo usuário (UDR) para rotear o tráfego dos pods para um dispositivo de rede virtual.
* Os pods podem acessar recursos na Internet pública. Também é um recurso de rede básica.

## <a name="advanced-networking-prerequisites"></a>Pré-requisitos de rede avançados

* A rede virtual do cluster do AKS deve permitir conectividade com a Internet de saída.
* Não crie mais de um cluster AKS na mesma sub-rede.
* Os clusters AKS não podem usar `169.254.0.0/16`, `172.30.0.0/16` ou `172.31.0.0/16` para o intervalo de endereços de serviço do Kubernetes.
* A entidade de serviço usada pelo cluster do AKS deve ter pelo menos permissões de [Colaborador de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede na rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de Colaborador de Rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planejar o endereçamento IP para o cluster

Os clusters configurados com a rede avançada requerem planejamento adicional. O tamanho da rede virtual e sub-rede deve acomodar tanto o número de pods que você planeja executar, como o número de nós para o cluster.

Endereços IP para os pods e os nós do cluster são atribuídos a partir da sub-rede especificada na rede virtual. Cada nó está configurado com um IP primário, que é o IP do nó e 30 endereços IP adicionais pré-configurados pelo Azure CNI que são atribuídos a pods agendados para o nó. Quando você expandir o cluster, cada nó está configurado da mesma forma com endereços IP da sub-rede.

O plano de endereço IP de um cluster do AKS consiste em uma rede virtual, pelo menos uma sub-rede para nós e pods, e um intervalo de endereços de serviço do Kubernetes.

| Intervalo de endereços/ recurso do Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual do Azure pode ser tão grande quanto /8, mas é limitada a 65.536 endereços IP configurados. |
| Sub-rede | Deve ser grande o suficiente para acomodar os nós, pods e todos os recursos do Kubernetes e do Azure que possam ser provisionados no cluster. Por exemplo, se você implantar um Azure Load Balancer interno, os IPs de front-end serão alocados da sub-rede do cluster, e não IPs públicos. <p/>Para calcular o tamanho *mínimo* de sub-rede: `(number of nodes) + (number of nodes * pods per node)` <p/>Exemplo para um cluster de 50 nós: `(50) + (50 * 30) = 1,550` (/21 ou maior) |
| Intervalo de endereços de serviço do Kubernetes | Esse intervalo não deve ser usado por nenhum elemento de rede ou conectado a essa rede virtual. O endereço de serviço CIDR deve ser menor do que /12. |
| Endereço IP do serviço DNS do Kubernetes | Endereços IP dentro do intervalo de endereços de serviço do Kubernetes que serão usados pela descoberta do serviço de cluster (kube-dns). |
| Endereço de ponte de docker | Endereço IP (em notação CIDR) usado como o endereço IP de ponte de docker em nós. Padrão de 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Máximo de pods por nó

O número máximo padrão de pods por nó em um cluster do AKS varia entre a rede Básica e Avançada e o método de implantação de cluster.

### <a name="default-maximum"></a>Máximo padrão

Esses são os máximos *padrão* quando você implanta um cluster AKS sem especificar o número máximo de pods no momento da implantação:

| Método de implantação | Basic | Avançado | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | SIM |
| Modelo do Resource Manager | 110 | 30 | SIM |
| Portal | 110 | 30 | Não  |

### <a name="configure-maximum---new-clusters"></a>Configurar o máximo - novos clusters

Para especificar um número máximo diferente de pods por nó ao implantar um cluster do AKS:

* **CLI do Azure**: especifica o argumento `--max-pods` ao implantar um cluster com o comando [az aks create][az-aks-create].
* **Modelo do Resource Manager**: especifica a propriedade `maxPods` no objeto [ManagedClusterAgentPoolProfile] ao implantar um cluster com um modelo do Resource Manager.
* **Portal do Azure**: não é possível modificar o número máximo de pods por nó ao implantar um cluster com o portal do Azure. Os clusters de rede avançados estão limitados a 30 pods por nó quando implantados no portal do Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar o máximo - clusters existentes

Não é possível alterar os pods máximos por nó em um cluster do AKS existente. O número poderá ser ajustado somente quando você implantar inicialmente o cluster.

## <a name="deployment-parameters"></a>Parâmetros de implantação

A criar um cluster do AKS, os parâmetros a seguir serão configuráveis para rede avançada:

**Rede virtual**: a rede virtual na qual você deseja implantar o cluster do Kubernetes. Se você quiser criar uma nova rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar rede virtual*. Para obter mais informações sobre limites e cotas para uma rede virtual do Azure, consulte [Limites, cotas e restrições de assinatura e serviço do Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sub-rede**: a sub-rede dentro da rede virtual em que você quer implantar o cluster. Se você quiser criar uma nova sub-rede na rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar sub-rede*.

**Intervalo de endereços do serviço do Kubernetes**: este é o conjunto de IPs virtuais que o Kubernetes atribui aos [serviços][services] do seu cluster. Você pode usar qualquer intervalo de endereço particular que atenda aos seguintes requisitos:

* Não deve estar dentro do intervalo de endereços IP da rede virtual do cluster
* Não deve se sobrepor a nenhuma outra rede virtual com a qual a rede virtual do cluster está emparelhada
* Não deve sobrepor IPs locais
* Não deve estar dentro dos intervalos `169.254.0.0/16`, `172.30.0.0/16` ou `172.31.0.0/16`

Embora seja tecnicamente possível especificar um intervalo de endereço de serviço na mesma rede virtual do cluster, isso não é recomendado. Um comportamento imprevisível pode ocorrer se forem usados intervalos de IP sobrepostos. Para mais informações, consulte a seção [FAQ](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços do Kubernetes, consulte [Serviços][services] na documentação do Kubernetes.

**Endereço IP do serviço DNS do Kubernetes**: O endereço IP para o serviço DNS do cluster. Esse endereço deve estar dentro do *intervalo de endereços do serviço do Kubernetes*.

**Endereço da ponte de docker**: O endereço IP e a máscara de rede para atribuir à ponte Docker. Esse endereço IP não deve estar dentro do intervalo de endereços IP da rede virtual do cluster.

## <a name="configure-networking---cli"></a>Configurar a rede – CLI

Quando você cria um cluster AKS com a CLI do Azure, também pode configurar os recursos avançados de rede. Use os comandos a seguir para criar um novo cluster AKS com recursos avançados de rede habilitados.

Primeiro, obtenha a ID de recurso de sub-rede para a sub-rede existente no qual o cluster AKS será adicionado:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use o comando [az aks create][az-aks-create] com o argumento `--network-plugin azure` para criar um cluster com os recursos avançados de rede. Atualize o valor `--vnet-subnet-id` com a ID de sub-rede obtida na etapa anterior:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurar a rede – portal

Captura de tela a seguir do portal do Azure mostra um exemplo de como configurar essas configurações durante a criação do cluster AKS:

![Configuração de rede avançada no portal do Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir se aplicam à configuração de rede **avançada**.

* *Posso implantar VMs na sub-rede do cluster?*

  Não. Não há suporte para a implantação de VMs na sub-rede usada por seu cluster Kubernetes. As VMs podem ser implantadas na mesma rede virtual, mas em uma sub-rede diferente.

* *Posso configurar políticas de rede por pod?*

  Não. Não há suporte para políticas de rede por pod no momento.

* *O número máximo de pods implantados em um nó é configurável?*

  Sim, ao implantar um cluster com a CLI do Azure ou um modelo do Resource Manager. Consulte [Máximo de pods por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de pods por nó em um cluster existente.

* *Como configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos de extremidade do serviço.*

  A lista completa de propriedades para a rede virtual e as sub-redes que você cria durante a criação do cluster do AKS pode ser configurada na página de configuração de rede virtual padrão no portal do Azure.

* *Eu posso usar uma sub-rede diferente na rede virtual do cluster para o*   **intervalo de endereços de serviço do Kubernetes**?

  Não é recomendado, mas essa configuração é possível. O intervalo de endereços de serviço é um conjunto de IPs virtuais (VIPs) que o Kubernetes atribui aos serviços em seu cluster. O Azure Networking não tem visibilidade do intervalo de IP de serviço do cluster do Kubernetes. Devido à falta de visibilidade do intervalo de endereços de serviço do cluster, é possível criar posteriormente uma nova sub-rede na rede virtual do cluster que se sobrepõe ao intervalo de endereços de serviço. Se tal sobreposição ocorrer, o Kubernetes poderá atribuir um serviço a um IP que já esteja em uso por outro recurso na sub-rede, causando comportamento ou falhas imprevisíveis. Ao garantir que você use um intervalo de endereços fora da rede virtual do cluster, é possível evitar esse risco de sobreposição.

## <a name="next-steps"></a>Próximas etapas

### <a name="networking-in-aks"></a>Rede no AKS

Saiba mais sobre a rede no AKS nos seguintes artigos:

[Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS).](static-ip.md)

[Entrada HTTPS no Serviço de Contêiner do Azure (AKS)](ingress.md)

[Usar um balanceador de carga interno com o Serviço de Contêiner do Azure (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Mecanismo do ACS

[Mecanismo do Serviço de Contêiner do Azure (Mecanismo de ACS)][acs-engine] é um projeto de código-fonte aberto que gera os modelos do Azure Resource Manager, você pode usar para implantar clusters habilitados para Docker no Azure. Kubernetes, DC/OS, modo Swarm e orquestradores Swarm podem ser implantados com Mecanismo do ACS.

Os clusters de Kubernetes criados com o Mecanismo do ACS dão suporte a plug-ins [kubenet][kubenet] e [Azure CNI][cni-networking]. Assim, cenários de rede básica e avançada são suportados pelo Mecanismo do ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
