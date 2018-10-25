---
title: Conceitos - Rede nos Serviços do Kubernetes do Azure (AKS)
description: Aprenda sobre a rede no Serviço de Kubernetes do Azure (AKS), incluindo redes básicas e avançadas, controladores de entrada, balanceadores de carga e endereços IP estáticos.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380483"
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

No Kubernetes, os *Serviços* agrupam logicamente os pods para permitir o acesso direto por meio de um endereço IP ou nome DNS e em uma porta específica. Você também pode distribuir tráfego usando um *balanceador de carga*. Um roteamento mais complexo de tráfego de aplicativos também pode ser obtido com os *Controladores de Ingressos*. A segurança e a filtragem do tráfego de rede para pods é possível com as políticas de *rede do Kubernetes*.

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

- *Rede Básica* - Os recursos de rede são criados e configurados à medida que o cluster AKS é implementado.
- *Rede avançada* - O cluster AKS é conectado a recursos e configurações de rede virtual existentes.

### <a name="basic-networking"></a>Rede básica

A opção de rede *básica* é a configuração padrão para a criação do cluster AKS. A plataforma do Azure gerencia a configuração de rede do cluster e dos pods. A rede básica é apropriada para implantações que não exigem configuração de rede virtual personalizada. Com a rede básica, você não pode definir a configuração de rede, como nomes de sub-rede ou os intervalos de endereços IP atribuídos ao cluster AKS.

Nós em um cluster AKS configurado para rede básica usam o [kubenet][kubenet] plugin do Kubernetes.

Rede básica fornece os seguintes recursos:

- Expor um serviço do Kubernetes externa ou internamente por meio do Azure Load Balancer.
- Os pods podem acessar recursos na Internet pública.

### <a name="advanced-networking"></a>Rede avançada

A *avançada* rede coloca seus pods em uma rede virtual do Azure que você configura. Essa rede virtual fornece conectividade automática a outros recursos do Azure e integração com um conjunto avançado de recursos. A rede avançada é apropriada para implantações que exigem configurações de rede virtual específicas, como o uso de uma sub-rede e conectividade existentes. Com a rede avançada, você pode definir esses nomes de sub-rede e intervalos de endereços IP.

Os nós em um cluster AKS configurado para rede avançada usam o [plug-in Kubernetes da interface de rede do recipiente do Azure (CNI)][cni-networking].

![Diagrama que mostra dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram]

A rede avançada fornece os seguintes recursos em relação à rede básica:

- Implemente seu cluster AKS em uma rede virtual existente do Azure ou crie uma nova rede virtual e sub-rede para seu cluster.
- Cada pod no cluster recebe um endereço IP na rede virtual. Os pods podem se comunicar diretamente com outros pods no cluster e outros nós na rede virtual.
- Um pod pode se conectar a outros serviços em uma rede virtual emparelhada, inclusive em redes locais por meio de conexões VPN ExpressRoute e site-a-site (S2S). Os pods também são acessíveis do local.
- Os pods em uma sub-rede com os terminais de serviço habilitados podem se conectar com segurança aos serviços do Azure, como o Armazenamento do Azure e o SQL DB.
- Você pode criar rotas definidas pelo usuário (UDR) para rotear o tráfego de pods para um Network Virtual Appliance.

Para obter mais informações, consulte [Configurar rede avançada para um cluster AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Controladores de entrada

Quando você cria um serviço do tipo LoadBalancer, um recurso de balanceador de carga subjacente do Azure é criado. O balanceador de carga é configurado para distribuir o tráfego para os pods em seu Serviço em uma determinada porta. O LoadBalancer só funciona na camada 4 - o Serviço não está ciente dos aplicativos reais e não pode fazer nenhuma outra consideração de roteamento.

*Os controladores ingressos* funcionam na camada 7 e podem usar regras mais inteligentes para distribuir o tráfego de aplicativos. Um uso comum de um controlador de ingresso é rotear o tráfego HTTP para diferentes aplicativos com base na URL de entrada.

![Diagrama mostrando o fluxo de tráfego de ingresso em um cluster AKS][aks-ingress]

No AKS, você pode criar um recurso do Ingress usando algo como o NGINX ou usar o recurso de roteamento do aplicativo AKS HTTP. Quando você habilita o roteamento de aplicativo HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *External-DNS*. À medida que novos recursos do Ingress são criados no Kubernetes, os registros necessários do DNS A são criados em uma zona DNS específica do cluster. Para obter mais informações, consulte [implantar o roteamento de aplicativos HTTP][aks-http-routing].

Outro recurso comum do Ingress é o encerramento de SSL / TLS. Em grandes aplicativos da Web acessados via HTTPS, a terminação do TLS pode ser manipulada pelo recurso Ingress, em vez de pelo próprio aplicativo. Para fornecer geração e configuração automática de certificação TLS, você pode configurar o recurso Ingress para usar provedores como o Let's Encrypt. Para obter mais informações sobre como configurar um controlador NGINX Ingress com o Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Grupos de segurança de rede

Uma segurança grupo filtrar tráfego de rede para VMs, como os nós do AKS. À medida que você cria Serviços, como um LoadBalancer, a plataforma do Azure configura automaticamente as regras do grupo de segurança de rede necessárias. Não configure manualmente as regras do grupo de segurança de rede para filtrar o tráfego de pods em um cluster do AKS. Defina as portas e o encaminhamento necessários como parte dos manifestos do Kubernetes Service e permita que a plataforma do Azure crie ou atualize as regras apropriadas.

Regras de grupo de segurança de rede padrão existem para o tráfego, como SSH. Essas regras padrão são para gerenciamento de cluster e acesso à solução de problemas. A exclusão dessas regras padrão pode causar problemas com o gerenciamento do AKS e interrompe o objetivo de nível de serviço (SLO).

## <a name="next-steps"></a>Próximas etapas

Para começar com a rede AKS, consulte [Criar e configurar a rede avançada para um cluster AKS][aks-configure-advanced-networking].

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
[aks-ingress-tls]: ingress.md
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md