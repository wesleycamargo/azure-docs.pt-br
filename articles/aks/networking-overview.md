---
title: Configuração de rede no Serviço do Kubernetes do Azure (AKS)
description: Saiba mais sobre a configuração de rede básica e avançada no Serviço de Kubernetes do Azure (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/04/2018
ms.author: marsma
ms.openlocfilehash: d6f42a5f3ce907fdb759bef29ca25bdc7fe365d9
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757001"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Configuração de rede no Serviço do Kubernetes do Azure (AKS)

Quando você cria um cluster Serviço do Kubernetes do Azure (AKS), você pode selecionar entre duas opções de rede: **Básica** ou **Avançada**.

## <a name="basic-networking"></a>Rede básica

A opção de rede **básica** é a configuração padrão para a criação de cluster AKS. A configuração de rede do cluster e seus pods são totalmente gerenciadas pelo Azure e é adequada para implantações que não necessitam de configuração de rede virtual personalizada. Você não tem controle sobre a configuração de rede, como sub-redes ou intervalos de endereço IP atribuídos ao cluster quando você seleciona a rede básica.

Os nós em um cluster AKS configurado para o uso básico de rede o plug-in do Kubernetes [kubenet][kubenet].

## <a name="advanced-networking"></a>Rede avançada

A rede **avançada** coloca seus pods em uma rede virtual do Azure (VNet) que você configura, fornecendo a eles conectividade automática para recursos de rede virtual e integração com o sofisticado conjunto de recursos que as VNets oferecem.
A rede avançada está atualmente disponível apenas ao implantar clusters AKS no [portal do Azure][portal] ou com um modelo do Resource Manager.

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

> [!IMPORTANT]
> Cada nó em um cluster AKS configurado para rede avançada pode hospedar um máximo de **30 pods**. Cada VNet provisionada para uso com o plug-in do Azure CNI é limitada a **4096 endereços IP configurados**.

## <a name="advanced-networking-prerequisites"></a>Pré-requisitos de rede avançados

* A VNet para o cluster AKS deve permitir a conectividade de Internet de saída.
* Não crie mais de um cluster AKS na mesma sub-rede.
* A rede avançada para AKS não é compatível com VNets que usam Zonas DNS Privadas do Azure.
* Os clusters AKS não podem usar `169.254.0.0/16`, `172.30.0.0/16` ou `172.31.0.0/16` para o intervalo de endereços de serviço do Kubernetes.
* A entidade de serviço usada para o cluster AKS deve ter permissões `Contributor` para o grupo de recursos que contém a VNet existente.

## <a name="plan-ip-addressing-for-your-cluster"></a>Planejar o endereçamento IP para o cluster

Os clusters configurados com a rede avançada requerem planejamento adicional. O tamanho da VNet e de sua sub-rede deve acomodar o número de pods que você planeja executar, assim como o número de nós para o cluster.

Os endereços IP para os pods e nós do cluster são atribuídos a partir da sub-rede especificada na VNet. Cada nó está configurado com um IP primário, que é o IP do nó e 30 endereços IP adicionais pré-configurados pelo Azure CNI que são atribuídos a pods agendados para o nó. Quando você expandir o cluster, cada nó está configurado da mesma forma com endereços IP da sub-rede.

O plano de endereço IP para um cluster AKS consiste em uma VNet, pelo menos uma sub-rede para nós e pods e um intervalo de endereços de serviço do Kubernetes.

| Intervalo de endereços/ recurso do Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A VNet do Azure pode ser de até /8, mas pode ter apenas 4096 endereços IP configurados. |
| Sub-rede | Deve ser grande o suficiente para acomodar os nós e os pods. Para calcular o tamanho mínimo da sub-rede: (Número de nós) + (Número de nós * Pods por nó). Para um cluster de 50 nós: (50) + (50 * 30) = 1.550, sua sub-rede precisaria ser /21 ou maior. |
| Intervalo de endereços de serviço do Kubernetes | Esse intervalo não deve ser usado por nenhum elemento de rede em uma VNet ou conectado a ela. O endereço de serviço CIDR deve ser menor do que /12. |
| Endereço IP do serviço DNS do Kubernetes | Endereços IP dentro do intervalo de endereços de serviço do Kubernetes que serão usados pela descoberta do serviço de cluster (kube-dns). |
| Endereço de ponte de docker | Endereço IP (em notação CIDR) usado como o endereço IP de ponte de docker em nós. Padrão de 172.17.0.1/16. |

Como mencionado anteriormente, cada VNet provisionada para uso com o plug-in do Azure CNI é limitada a **4096 endereços IP configurados**. Cada nó em um cluster configurado para rede avançada pode hospedar um máximo de **30 pods**.

## <a name="configure-advanced-networking"></a>Configurar a rede avançada

Quando você [cria um cluster AKS](kubernetes-walkthrough-portal.md) no portal do Azure, os seguintes parâmetros são configuráveis para a rede avançada:

**Rede virtual**: A VNet no qual você deseja implantar o cluster do Kubernetes. Se você quiser criar uma nova VNet para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar rede virtual*.

**Subrede**: A sub-rede na VNet onde você deseja implantar o cluster. Se você quiser criar uma nova sub-rede na VNet para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar sub-rede*.

**Intervalo de endereços de serviço do Kubernetes**: O intervalo de endereços IP para os IPs de serviço do cluster do Kubernetes. Este intervalo não deve ser dentro do intervalo de endereço IP VNet do cluster.

**Endereço IP do serviço DNS do Kubernetes**: O endereço IP para o serviço DNS do cluster. Esse endereço deve estar dentro do *intervalo de endereços do serviço do Kubernetes*.

**Endereço da ponte de docker**: O endereço IP e a máscara de rede para atribuir à ponte Docker. Este endereço IP não deve estar dentro do intervalo de endereço IP VNet do cluster.

Captura de tela a seguir do portal do Azure mostra um exemplo de como configurar essas configurações durante a criação do cluster AKS:

![Configuração de rede avançada no portal do Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir se aplicam à configuração de rede **avançada**.

* *Posso configurar rede avançada com a CLI do Azure?*

  Nº A rede avançada está atualmente disponível apenas ao implantar clusters AKS no portal do Azure ou com um modelo do Resource Manager.

* *Posso implantar VMs na sub-rede do cluster?*

  Nº Não há suporte para a implantação de VMs na sub-rede usada por seu cluster Kubernetes. As VMs podem ser implantadas na mesma VNet, mas em uma sub-rede diferente.

* *Posso configurar políticas de rede por pod?*

  Nº Não há suporte para políticas de rede por pod no momento.

* *O número máximo de pods implantados em um nó é configurável?*

  Por padrão, cada nó pode hospedar um máximo de 30 pods. Você pode alterar o valor máximo apenas modificando a propriedade `maxPods` ao implantar um cluster com um modelo do Resource Manager.

* *Como configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos de extremidade do serviço.*

  A lista completa de propriedades para a VNet e sub-redes que você criar durante a criação do cluster AKS pode ser configurada na página de configuração de VNet padrão no portal do Azure.

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
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
