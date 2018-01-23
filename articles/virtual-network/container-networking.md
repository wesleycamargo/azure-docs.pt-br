---
title: "Rede Virtual do Azure para contêineres | Microsoft Docs"
description: "Saiba mais sobre o plug-in CNI para clusters Kubernetes, que permite que os contêineres se comuniquem entre si e com outros recursos, em uma rede virtual."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Rede de contêineres

O Azure fornece um [plug-in CNI (Interface de rede de contêineres)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) que permite implantar e gerenciar seu próprio cluster Kubernetes, com capacidade de rede nativa do Azure. O plug-in é habilitado, por padrão, ao implantar clusters Kubernetes com o [Mecanismo do Serviço de Contêiner do Azure](https://github.com/Azure/acs-engine) (ou mecanismo de ACS).

## <a name="networking-capabilities"></a>Recursos de rede

Os contêineres podem utilizar o conjunto avançado de recursos que uma rede virtual oferece, como:
-   Você pode criar uma rede virtual separada para o cluster ou implantar o cluster em uma rede virtual existente. 
-   Cada pod no cluster recebe um endereço IP dentro da rede virtual e pode se comunicar diretamente com outros pods no cluster e em qualquer máquina virtual na rede virtual. 
-   Um pod pode se conectar com outros pods e máquinas virtuais em redes virtuais pares e com redes locais, em conexões VPN site a site e do ExpressRoute. Os recursos locais podem se comunicar com os pods. 
-   Você pode expor um serviço do Kubernetes à Internet por meio do Azure Load Balancer.  
-   Os pods em uma sub-rede que tem pontos de extremidade de serviço habilitados podem se conectar com segurança aos serviços do Azure (armazenamento e banco de dados SQL, por exemplo).
-   Você pode usar rotas definidas pelo usuário para rotear o tráfego dos pods para um dispositivo de rede virtual. 
-   Os pods podem acessar recursos públicos na Internet.
-   Você pode atribuir um endereço IP público a um pod, que pode ser associado a um nome DNS.
 
## <a name="limits"></a>limites
Ao usar o plug-in, você pode implantar até 4 mil nós em um cluster Kubernetes e até 250 pods por nó, com um limite total de 16 mil pods por cluster.

## <a name="constraints"></a>Restrições
- O plug-in não é habilitado ao implantar um cluster Kubernetes com o [AKS (Serviço de Contêiner do Azure)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou o cluster [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) com o Kubernetes.
- Não há nenhum suporte nativo para políticas de rede do Kubernetes, incluindo políticas de acesso ou de DNS.
- O plug-in não é compatível com políticas de rede por pod.

## <a name="pricing"></a>Preços
O uso do plug-in CNI não é cobrado.

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar um cluster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) em sua [própria rede virtual](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
