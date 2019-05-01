---
title: VLANs e sub-redes na solução do VMware CloudSimple - Azure
description: Saiba mais sobre VLANs e sub-redes em uma nuvem privada de CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 448b2b51ef6c5f7aa0bd660eec580019714caf5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577924"
---
# <a name="vlans-and-subnets-overview"></a>Visão geral de VLANs e sub-redes

CloudSimple fornece uma rede por região em que o seu serviço CloudSimple é implantado.  A rede é um único espaço de endereço de TCP de camada 3 com roteamento ativado por padrão.  Todas as nuvens privadas e as sub-redes criadas nessa região podem se comunicar entre si sem qualquer configuração adicional.  Você pode criar grupos de portas distribuído no vCenter usando as VLANs.

## <a name="vlans"></a>VLANs

VLANs (rede de camada 2) são criados por nuvem privada.  O tráfego de camada 2 permanece dentro dos limites de uma nuvem privada, permitindo que você isole o tráfego local na nuvem particular.  Uma VLAN criada na nuvem privada pode ser usada para criar grupos de portas distribuído somente em nuvem privada.  Uma VLAN criada em uma nuvem privada é configurada automaticamente em todos os comutadores conectados aos hosts de uma nuvem privada.

## <a name="subnets"></a>Sub-redes

Você pode criar uma sub-rede quando você cria uma VLAN, definindo o espaço de endereço da sub-rede. Um endereço IP do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço de camada 3 privado é atribuído para cada cliente e região. Você pode configurar qualquer espaço de endereços RFC 1918 não sobrepostas, com sua rede local ou uma rede virtual do Azure, em sua região de rede.

Todas as sub-redes podem se comunicar entre si por padrão, reduzindo a configuração de sobrecarga para roteamento entre nuvens privadas. Leste-Oeste dados em PCs na mesma região permanecem na mesma rede de camada 3 e transfere sobre a infraestrutura de rede local dentro da região. Nenhuma saída é necessária para a comunicação entre nuvens privadas em uma região. Essa abordagem elimina qualquer penalidade de desempenho da WAN/saída na implantação de cargas de trabalho diferentes em nuvens privadas diferentes.

## <a name="vspherevsan-subnets-cidr-range"></a>intervalo CIDR de sub-redes vSphere/vSAN

Uma nuvem privada é criada como uma pilha de VMware isolada (hosts ESXi, vCenter, vSAN e NSX) ambiente gerenciado por um servidor vCenter.  Componentes de gerenciamento são implantados na rede selecionada para **vSphere/vSAN sub-redes CIDR**.  A intervalo de CIDR de rede é dividida em sub-redes diferentes durante a implantação.

Prefixo de intervalo mínimo vSphere/vSAN sub-redes CIDR: **/24** prefixo de intervalo de sub-redes de vSphere/vSAN máximo CIDR:   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de intervalo de sub-redes vSphere/vSAN CIDR

Selecionar o tamanho do intervalo CIDR de sub-redes vSphere/vSAN tem um impacto no tamanho da sua nuvem privada.  Tabela a seguir mostra o número máximo de nós que você pode ter com base no tamanho do vSphere/vSAN sub-redes CIDR.

| Comprimento do prefixo especificadas vSphere/vSAN sub-redes CIDR | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subredes de gerenciamento criados em uma nuvem privada

Seguintes sub-redes de gerenciamento são criadas quando você cria uma nuvem privada. 

* **Gerenciamento de sistema** -VLAN e sub-rede para o gerenciamento de hosts de ESXi de rede, servidor DNS, servidor do vCenter.
* **VMotion** -VLAN e sub-rede para vMotion rede hosts de ESXi.
* **VSAN** -VLAN e sub-rede para vSAN rede hosts de ESXi.
* **NsxtEdgeUplink1** -VLAN e sub-rede para uplinks VLAN a uma rede externa.
* **NsxtEdgeUplink2** -VLAN e sub-rede para uplinks VLAN a uma rede externa.
* **NsxtEdgeTransport** -VLAN e sub-rede para as zonas de transporte controlam o alcance de redes de camada 2 no NSX-T.
* **NsxtHostTransport** -VLAN e sub-rede para o host de transporte de zona.

### <a name="management-network-cidr-range-breakdown"></a>Divisão de intervalo CIDR de rede de gerenciamento

intervalo CIDR de vSphere/vSAN sub-redes especificado é dividido em várias sub-redes.  Tabela a seguir mostra um exemplo de como a divisão para prefixos permitidos.  O exemplo utiliza **192.168.0.0** como o intervalo CIDR.

Exemplo:

| Especificado CIDR/prefixo de subredes vSphere/vSAN | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gerenciamento do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transporte de Host NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte de borda NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| T NSX borda Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| T NSX borda uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar VLANs e sub-redes](https://docs.azure.cloudsimple.com/vlansubnet/)