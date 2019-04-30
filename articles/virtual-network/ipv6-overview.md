---
title: Visão geral do IPv6 para a rede Virtual do Azure (visualização)
titlesuffix: Azure Virtual Network
description: Descrição de IPv6 de caminhos de dados e os pontos de extremidade IPv6 em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130833"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>O que é o IPv6 para a rede Virtual do Azure? (Visualização)

IPv6 para Azure VNET (rede Virtual) permite que você hospedar aplicativos no Azure com conectividade IPv6 e IPv4 em uma rede virtual e para e da Internet. Devido ao esgotamento de endereços IPv4 públicos, novas redes para mobilidade e a Internet das coisas (IoT) normalmente são criadas em IPv6. Até mesma longa estabelecida ISP e redes móveis estão sendo transformadas para o IPv6. Serviços compatíveis somente com IPv4 podem encontrar em si em desvantagem real em mercados emergentes e existentes. Conectividade IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure percorrer essa lacuna de tecnologia com os serviços de disponíveis globalmente, com pilha dupla prontamente, conecte-se com o IPv4 existente e esses novos dispositivos de IPv6 e redes.

Conectividade IPv6 da original do Azure torna fácil fornecer conectividade com a Internet (IPv4/IPv6) de pilha dupla para aplicativos hospedados no Azure. Ele permite a implantação simple de VMs com conectividade de IPv6 com balanceamento de carga para conexões iniciadas de entrada e saídas. Esse recurso ainda está disponível e mais informações estão disponíveis [aqui](../load-balancer/load-balancer-ipv6-overview.md).
O IPv6 para a rede virtual do Azure é muito mais completo em destaque-habilitando arquiteturas de solução completas do IPv6 a ser implantado no Azure.

> [!Important]
> O IPv6 para a rede Virtual do Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

O diagrama a seguir ilustra uma implantação simples de pilha dual (IPv4/IPv6) no Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

Benefícios do IPv6 de rede Virtual do Azure:

- Ajuda a expandir o alcance de seus aplicativos hospedados no Azure para a crescente móveis e mercados de Internet das coisas.
- Duas VMs de IPv4/IPv6 empilhadas fornecem flexibilidade de implantação máxima do serviço. Uma única instância de serviço pode se conectar com clientes de IPv4 e Internet compatível com IPv6.
- Se baseia no acanhado e estável do Azure-para-à Internet pela VM conectividade IPv6.
- Seguro por padrão, uma vez que a conectividade IPv6 com a Internet somente será estabelecida quando você o solicitar explicitamente em sua implantação.

## <a name="capabilities"></a>Funcionalidades

Suporte a IPv6 para máquinas virtuais inclui os seguintes recursos:

- Os clientes do Azure podem definir seu próprio espaço de endereço de rede virtual do IPv6 para atender às necessidades de seus aplicativos, os clientes, ou integrar perfeitamente em seu espaço IP local.
- Pilha dual (IPv4 e IPv6) redes virtuais com sub-redes de pilha dupla permitem que aplicativos conectem-se com os recursos de IPv4 e IPv6 na sua rede virtual ou - a Internet.
- Proteger seus recursos com as regras de IPv6 para os grupos de segurança de rede
- Personalize o roteamento de tráfego IPv6 em sua rede virtual com definidas pelo usuário rotas - especialmente ao aproveitar os dispositivos de rede Virtual para ampliar seu aplicativo.
- Suporte de Balanceador de carga do IPv6 para criar aplicativos escalonáveis e resilientes que inclui suporte de DNS do Azure para registros AAAA para IPs públicos do IPv6.
- Adicione facilmente conectividade IPv6 para as implantações existentes somente IPv4 com a atualização no local.
- Crie aplicativos de pilha dual que dimensione automaticamente para sua carga de conjuntos de dimensionamento de máquina virtual.

## <a name="limitations"></a>Limitações
A versão de visualização do IPv6 para a rede virtual do Azure tem as seguintes limitações:
- O IPv6 para a rede virtual do Azure (visualização) está disponível em todas as regiões globais do Azure, mas somente no Global Azure - não as nuvens governamentais.   
- Suporte para a versão prévia do portal é limitado a exibir apenas para configuração de IPv6 muito, mas não todas, no entanto, o IPv6 para a rede virtual tem suporte completo e a documentação (com exemplos) para a implantação do IPv6 usando o Powershell e a Interface de linha de comando (CLI).
- Suporte do observador de rede para a visualização é limitado a logs de fluxo NSG e capturas de pacote de rede.
- Suporte de balanceamento de carga para a visualização é limitado inicialmente ao balanceador de carga básico.
- IPs públicos em nível de instância (IPs públicos diretamente em uma máquina virtual) não tem suporte na versão prévia.  
- (Emparelhamento de rede virtual regional ou globalmente) não é suportado na versão prévia. 

## <a name="pricing"></a>Preços

Largura de banda e recursos do IPv6 do Azure são cobrados nas mesmas taxas como IPv4. Não há encargos adicionais ou diferentes para IPv6. Você pode encontrar detalhes sobre os preços para [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/), ou [balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
