---
title: Integrar o Firewall do Azure com o Azure Standard Load Balancer
description: Saiba como integrar o Firewall do Azure com o Azure Standard Load Balancer
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193742"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Firewall do Azure com o Azure Standard Load Balancer

Você pode integrar um Firewall do Azure em uma rede virtual com um Azure Standard Load Balancer (público ou interno). 

O design preferencial é integrar o balanceador de carga interno com o firewall do Azure, como este é um design muito mais simples. Se você já tiver um implantado e você quiser mantê-lo no lugar, você pode usar um balanceador de carga. No entanto, você precisa estar ciente de um problema de roteamento assimétrico que pode interromper a funcionalidade com o cenário de balanceador de carga público.

Para obter mais informações sobre o Azure Load Balancer, confira [O que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um balanceador de carga público, o balanceador de carga é implantado com um endereço IP de front-end público.

### <a name="asymmetric-routing"></a>Roteamento assimétrico

O roteamento assimétrico ocorre quando um pacote usa um caminho para o destino e outro caminho para retornar para a origem. Isso ocorre quando uma sub-rede tem uma rota padrão que vai para o endereço IP privado do firewall e você está usando um balanceador de carga público. Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoração de estado, ele descarta o pacote de retorno porque o firewall não está ciente de tal sessão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema de roteamento

Quando você implanta um Firewall do Azure em uma sub-rede, uma etapa é criar uma rota padrão para a sub-rede, direcionando os pacotes por meio do endereço IP privado do firewall localizado no AzureFirewallSubnet. Para obter mais informações, confira [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduz o firewall em seu cenário de balanceador de carga, você deseja que o tráfego de Internet entre por meio do endereço IP público do firewall. Daí, o firewall aplica suas regras de firewall e NATs aos pacotes para o endereço IP público do balanceador de carga. E o problema ocorre aí. Os pacotes chegam no endereço IP público do firewall, mas retornam para o firewall por meio do endereço IP privado (usando a rota padrão).
Para evitar esse problema, crie uma rota de host adicional para o endereço IP público do firewall. Pacotes que vão para o endereço IP público do firewall são roteadas pela Internet. Isso evita o uso da rota padrão para o endereço IP privado do firewall.

![Roteamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

Por exemplo, as seguintes rotas são para um firewall no endereço IP público 13.86.122.41 e o endereço IP privado 10.3.1.4.

![Tabela de rotas](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um balanceador de carga interno, o balanceador de carga é implantado com um endereço IP de front-end privado.

Não há nenhum problema de roteamento assimétrico com esse cenário. Os pacotes de entrada chegam ao endereço IP público do firewall, são convertidos para o endereço IP privado do balanceador de carga e, em seguida, retornam para o endereço IP privado do firewall usando o mesmo caminho de retorno.

Portanto, você pode implantar este cenário de forma semelhante ao cenário do balanceador de carga público, mas sem a necessidade da rota de host com endereço IP público do firewall.

## <a name="additional-security"></a>Segurança adicional

Para melhorar ainda mais a segurança do seu cenário de balanceamento de carga, você pode usar NSGs (grupos de segurança de rede).

Por exemplo, você pode criar um NSG na sub-rede de back-end em que se encontram as máquinas virtuais de balanceamento de carga. Permita o tráfego de entrada provenientes do endereço IP/porta do firewall.

Para obter mais informações sobre os NSGs, confira [Grupos de segurança](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).