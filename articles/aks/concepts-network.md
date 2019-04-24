---
title: Conceitos - Rede nos Serviços do Kubernetes do Azure (AKS)
description: Aprenda sobre a rede no AKS (Serviço de Kubernetes do Azure), incluindo as redes da CNI do Azure e kubenet e seus controladores de entrada, balanceadores de carga e endereços IP estáticos.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 02/28/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: cbdbf7dcd6269991d23c61d316dcee68e6678171
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467202"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicativos no Serviço de Kubernetes do Azure (AKS)

Em uma abordagem de micros serviços baseada em contêiner para o desenvolvimento de aplicativos, os componentes de aplicativo devem trabalhar juntos para processar suas tarefas. O Kubernetes fornece vários recursos que permitem a comunicação desse aplicativo. Você pode se conectar e expor aplicativos internamente ou externamente. Para criar aplicativos altamente disponíveis, você pode balancear a carga de seus aplicativos. Aplicativos mais complexos podem exigir configuração de tráfego de ingresso para terminação SSL / TLS ou roteamento de múltiplos componentes. Por motivos de segurança, você também pode precisar restringir o fluxo de tráfego de rede em ou entre pods e nós.

Este artigo apresenta os principais conceitos que fornecem rede para seus aplicativos no AKS:

- [Serviços](#services)
- [Redes virtuais do Azure](#azure-virtual-networks)
- [Controladores de entrada](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso aos seus aplicativos, ou para os componentes do aplicativo se comunicarem uns com os outros, o Kubernetes fornece uma camada de abstração para a rede virtual. Os nós do Kubernetes estão conectados a uma rede virtual e podem fornecer conectividade de entrada e saída para pods. O componente *kube-proxy* é executado em cada nó para fornecer esses recursos de rede.

No Kubernetes, os *Serviços* agrupam logicamente os pods para permitir o acesso direto por meio de um endereço IP ou nome DNS e em uma porta específica. Você também pode distribuir tráfego usando um *balanceador de carga*. Um roteamento mais complexo de tráfego de aplicativos também pode ser obtido com os *Controladores de Ingressos*. Segurança e a filtragem de tráfego de rede para pods é possível com o Kubernetes *políticas de rede* (em visualização no AKS).

A plataforma do Azure também ajuda a simplificar a rede virtual para clusters do AKS. Quando você cria um balanceador de carga do Kubernetes, o recurso do balanceador de carga do Azure subjacente é criado e configurado. À medida que você abre portas de rede para pods, as regras correspondentes do grupo de segurança de rede do Azure são configuradas. Para o roteamento de aplicativos HTTP, o Azure também pode configurar *DNS externo* à medida que novas rotas de ingresso são configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração de rede para cargas de trabalho de aplicativos, o Kubernetes usa *Serviços* para agrupar logicamente um conjunto de pods e fornecer conectividade de rede. Os seguintes tipos de serviço estão disponíveis:

- **IP do cluster** - Cria um endereço IP interno para uso no cluster do AKS. Bom para aplicativos internos que suportam outras cargas de trabalho no cluster.

    ![Diagrama mostrando o fluxo de tráfego IP do cluster em um cluster AKS][aks-clusterip]

- **NodePort** - Cria um mapeamento de porta no nó subjacente que permite que o aplicativo seja acessado diretamente com o endereço IP e a porta do nó.

    ![Diagrama mostrando o fluxo de tráfego do NodePort em um cluster AKS][aks-nodeport]

- **LoadBalancer** - Cria um recurso do balanceador de carga do Azure, configura um endereço IP externo e conecta os pods solicitados ao pool de back-ends do balanceador de carga. Para permitir que o tráfego de clientes alcance o aplicativo, as regras de balanceamento de carga são criadas nas portas desejadas. 

    ![Diagrama mostrando o fluxo de tráfego do Balanceador de Carga em um cluster AKS][aks-loadbalancer]

    Para controle adicional e roteamento do tráfego de entrada, você pode usar um [controlador de ingresso](#ingress-controllers).

- **ExternalName** - Cria uma entrada de DNS específica para facilitar o acesso a aplicativos.

O endereço IP para balanceadores de carga e serviços pode ser atribuído dinamicamente ou você pode especificar um endereço IP estático existente para uso. Endereços IP estáticos internos e externos podem ser atribuídos. Esse endereço IP estático existente é geralmente vinculado a uma entrada DNS.

Ambos *interno* e *externo* balanceadores de carga podem ser criados. Balanceadores de carga interno só são atribuídos a um endereço IP privado, portanto, não pode ser acessado da Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais do Azure

No AKS, você pode implantar um cluster que usa um dos dois modelos de rede a seguir:

- Rede *Kubenet* – os recursos de rede normalmente são criados e configurados à medida que o cluster AKS é implantado.
- Rede da *CNI (Interface de rede de contêiner) do Azure* – o cluster AKS é conectado a recursos e configurações de rede virtual existentes.

<a name="kubenet-basic-networking"></a>
### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação do cluster AKS. Com o *kubenet*, os nós obtêm um endereço IP de uma sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente da sub-rede da rede virtual do Azure dos nós. A NAT (conversão de endereços de rede), então, é configurada para que os pods possam acessar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é configurado via NAT como o endereço IP primário do nó.

Os nós usam o plug-in [kubenet][kubenet] do Kubernetes. Você pode permitir que a plataforma do Azure crie e configure as redes virtuais para você ou pode optar por implantar o cluster do AKS em uma sub-rede da rede virtual existente. Novamente, somente os nós recebem um endereço IP roteável e os pods usam NAT para se comunicar com outros recursos fora do cluster do AKS. Essa abordagem reduz muito o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Para obter mais informações, consulte [Configurar rede kubenet para um cluster AKS][aks-configure-kubenet-networking].

<a name="azure-cni-advanced-networking"></a>
### <a name="azure-cni-advanced-networking"></a>Rede da CNI do Azure (avançada)

Com a CNI do Azure, cada pod obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP precisam ser exclusivos no espaço de rede e ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem exige mais planejamento e, muitas vezes, leva à exaustão do endereço IP ou à necessidade de recriar os clusters em uma sub-rede maior conforme as demandas de aplicativo aumentam.

Nós usam o plug-in de [CNI (Interface de rede de contêiner) do Azure][cni-networking] do Kubernetes.

![Diagrama que mostra dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram]

A CNI do Azure fornece os seguintes recursos pela rede kubenet:

- Cada pod no cluster recebe um endereço IP na rede virtual. Os pods podem se comunicar diretamente com outros pods no cluster e outros nós na rede virtual.
- Os pods em uma sub-rede com os terminais de serviço habilitados podem se conectar com segurança aos serviços do Azure, como o Armazenamento do Azure e o SQL DB.
- Você pode criar rotas definidas pelo usuário (UDR) para rotear o tráfego de pods para um Network Virtual Appliance.

Para obter mais informações, consulte [Configurar a CNI do Azure para um cluster AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Controladores de entrada

Quando você cria um serviço do tipo LoadBalancer, um recurso de balanceador de carga subjacente do Azure é criado. O balanceador de carga é configurado para distribuir o tráfego para os pods em seu Serviço em uma determinada porta. O LoadBalancer só funciona na camada 4 - o Serviço não está ciente dos aplicativos reais e não pode fazer nenhuma outra consideração de roteamento.

*Os controladores ingressos* funcionam na camada 7 e podem usar regras mais inteligentes para distribuir o tráfego de aplicativos. Um uso comum de um controlador de ingresso é rotear o tráfego HTTP para diferentes aplicativos com base na URL de entrada.

![Diagrama mostrando o fluxo de tráfego de ingresso em um cluster AKS][aks-ingress]

No AKS, você pode criar um recurso do Ingress usando algo como o NGINX ou usar o recurso de roteamento do aplicativo AKS HTTP. Quando você habilita o roteamento de aplicativo HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *External-DNS*. À medida que novos recursos do Ingress são criados no Kubernetes, os registros necessários do DNS A são criados em uma zona DNS específica do cluster. Para obter mais informações, consulte [implantar o roteamento de aplicativos HTTP][aks-http-routing].

Outro recurso comum do Ingress é o encerramento de SSL / TLS. Em grandes aplicativos da Web acessados via HTTPS, a terminação do TLS pode ser manipulada pelo recurso Ingress, em vez de pelo próprio aplicativo. Para fornecer geração e configuração automática de certificação TLS, você pode configurar o recurso Ingress para usar provedores como o Let's Encrypt. Para obter mais informações sobre como configurar um controlador NGINX Ingress com o Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs, como os nós do AKS. À medida que você cria Serviços, como um LoadBalancer, a plataforma do Azure configura automaticamente as regras do grupo de segurança de rede necessárias. Não configure manualmente as regras do grupo de segurança de rede para filtrar o tráfego de pods em um cluster do AKS. Defina as portas e o encaminhamento necessários como parte dos manifestos do Kubernetes Service e permita que a plataforma do Azure crie ou atualize as regras apropriadas. Você também pode usar políticas de rede, conforme discutido na próxima seção, para aplicar automaticamente as regras de filtro de tráfego com os pods.

## <a name="network-policies"></a>Políticas de rede

Por padrão, todos os pods em um cluster AKS podem enviar e receber tráfego sem limitações. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente só são expostos aos serviços de front-end necessários ou os componentes de banco de dados são acessíveis somente para as camadas de aplicativos que se conectam a eles.

Política de rede é um recurso do Kubernetes atualmente em visualização no AKS que lhe permite controlar o fluxo de tráfego entre os pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos atribuídos, namespace ou porta de tráfego. Os grupos de segurança de rede são mais para os nós do AKS, e não para os pods. O uso de políticas de rede é uma maneira mais adequada e nativa da nuvem de controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente.

Para saber mais, confira [Proteger o tráfego entre os pods usando as políticas de rede no AKS (Serviço de Kubernetes do Azure)][use-network-policies].

## <a name="next-steps"></a>Próximos passos

Para obter uma introdução à rede do AKS, crie e configure um cluster do AKS com seus próprios intervalos de endereços IP usando [kubenet][aks-configure-kubenet-networking] ou a [CNI do Azure][aks-configure-advanced-networking].

Para práticas recomendadas associadas, consulte [práticas recomendadas para conectividade de rede e segurança no AKS][operator-best-practices-network].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Kubernetes / clusters AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / AKS de acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / escala de AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md

<!--Mooncake : URL redirect to ingress-tls.md-->

[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md