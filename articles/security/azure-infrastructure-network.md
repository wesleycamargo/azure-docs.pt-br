---
title: Arquitetura de rede do Azure
description: Este artigo fornece uma descrição geral da rede de produção Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101177"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede do Azure
A arquitetura de rede do Azure segue uma versão modificada do setor distribuição/Core/acesso modelo padrão, com as camadas de hardware diferentes. As camadas incluem:

- Núcleo (roteadores de Datacenter)
- Distribuição (roteadores de acesso e agregação de L2) – camada de distribuição separa o roteamento de L3 da alternância de L2
- Acesso (L2 Opções de Host)

A arquitetura de rede possui dois níveis de switches da camada 2, uma camada que agrega o tráfego da outra e loops da camada 2 para incorporar a redundância. Isso oferece benefícios, como uma superfície VLAN mais flexível e melhora o dimensionamento de porta. A arquitetura mantém L2 e L3 distintas, que permite o uso de hardware em cada uma das camadas distintas na rede e minimiza a falha em uma camada não afetem a outra camada (s). Permite o uso de troncos para compartilhamento de recursos, como a conectividade com a infraestrutura de L3.

## <a name="network-configuration"></a>Configuração de rede
A arquitetura de rede de um cluster do Microsoft Azure em um data center consiste dos seguintes dispositivos:

- Roteadores (Datacenter, o roteador de acesso e roteadores de borda de folha)
- Opções (agregação e superior de comutadores de Rack)
- Digi CMs
- PDUs ou Nucleons

A figura abaixo fornece uma visão geral da arquitetura de rede do Azure dentro de um cluster. O Azure tem duas arquiteturas separadas. Alguns clientes do Azure existentes e os serviços compartilhados residem na arquitetura de rede local padrão (DLA), enquanto as novas regiões e clientes virtuais serão acessados por meio de arquitetura de Quantum 10 (P10). A arquitetura do DLA é um projeto de árvore tradicional com roteadores de acesso passivos ativos e ACLs de segurança aplicados aos roteadores de acesso. A arquitetura do Quantum 10 é um design de clos/malha de roteadores em que as ACLs não são aplicadas nos roteadores, mas abaixo o roteamento por meio do Software Load Balancing (SLB) ou VLANs definida pelo software.

![Rede do Azure][1]

### <a name="quantum-10-devices"></a>Quantum 10 dispositivos
O design de Quantum 10 conduz a disseminação de comutação de camada 3 por vários dispositivos em um design de malha/CLOS. As vantagens do design P10 incluem maior funcionalidade e maior capacidade de dimensionar a infraestrutura de rede existente. O design emprega topo de Rack roteadores para passar o tráfego para clusters em várias rotas, permitindo a tolerância a falhas, lombada comutadores e roteadores de borda de folha. Segurança de serviços, como a conversão de endereços de rede são manipulados por meio de balanceamento de carga de Software em vez de em dispositivos de hardware.

### <a name="access-routers"></a>Roteadores de acesso
Os roteadores de acesso/distribuição L3 (ARs) executam a funcionalidade de roteamento principal para as camadas de acesso e distribuição. Esses dispositivos são implantados como um par e são o gateway padrão para as sub-redes. Cada par de AR pode dar suporte a vários pares de comutador de agregação de L2, dependendo da capacidade. O número máximo depende na capacidade do dispositivo, bem como os domínios de falha. Uma capacidade esperada com base em número típica seria três pares de comutador de agregação de L2 por par de AR.

### <a name="l2-aggregation-switches"></a>L2 Comutadores de Agregação  
Esses dispositivos servem como um ponto de agregação para o tráfego de L2. Eles são a camada de distribuição para a malha de L2 e podem lidar com grandes quantidades de tráfego. Como esses dispositivos agregam tráfego, a funcionalidade 802.1q é necessária e tecnologias de alta largura de banda, como agregação de portas e 10GE, são usadas.

### <a name="l2-host-switches"></a>L2 Opções de Host
Hosts se conecta diretamente a essas opções. Eles podem ser montados no rack comutadores ou implantações do chassi. O padrão de 802.1q permite a designação de uma VLAN como uma VLAN nativa, tratando VLAN como normal quadros Ethernet (sem marcas). Em circunstâncias normais, os quadros na VLAN nativa são transmitidos e recebidos sem marcas em um 802.1q porta de tronco. Esse recurso foi projetado para a migração para 802.1q e compatibilidade com non-802.1q dispositivos habilitados. Nessa arquitetura, apenas a infraestrutura de rede utiliza a VLAN nativa.

Essa arquitetura especifica um padrão para a seleção de VLAN nativa que garante, quando possível, que os dispositivos AR tenham uma VLAN nativa exclusiva para cada tronco e os troncos L2Aggregation para L2Aggregation. O L2Aggregation para troncos L2Host Switch tiver uma VLAN nativa de não-padrão.

### <a name="link-aggregation-8023ad"></a>Link Aggregation (802.3ad)
Agregação de link (LATÊNCIA) permite que vários links individuais para ser agrupadas e tratadas como um único link lógico. O número usado para designar as interfaces de canal de porta precisa ser padronizado para facilitar a depuração operacional, o resto da rede usará o mesmo número em ambas as extremidades de um canal de porta. Isso requer um padrão para determinar quais final do canal de porta para usar para definir o próximo número disponível.

Os números especificados para o L2Agg ao comutador L2Host são os números de porta de canal usados no lado do L2Agg. Como o intervalo de números é mais limitado no lado L2Host, o padrão é usar os números 1 e 2 no lado L2Host para se referir ao canal de porta que vai para o lado “a” e o “b” respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura de rede usa VLANs para servidores de grupo juntos em um único domínio de difusão. Números VLAN estão em conformidade com 802.1q padrões, que dá suporte a VLANs numeradas de 1 – 4094.

### <a name="customer-vlans"></a>Cliente VLANS
Os clientes têm várias opções de implementação de VLAN eles podem implantar por meio do Portal do Azure para atender às necessidades de separação e arquitetura de sua solução. Essas soluções são implantadas por meio de máquinas virtuais. Para exemplos de arquitetura de referência do cliente, visite [ Arquiteturas de Referência do Azure ](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Arquitetura de Edge
Data centers do Azure baseiam-se a infraestruturas de rede bem provisionada e altamente redundantes. As redes dentro dos datacenters do Azure são implementadas com "necessidade de mais de um" (N + 1) as arquiteturas de redundância ou melhor. Os recursos completos de failover dentro e entre os datacenters ajudam a garantir a disponibilidade da rede e do serviço. Externamente, os datacenters são servidos por circuitos de rede de alta largura de banda dedicados que conectam de forma redundante propriedades com mais de 1200 provedores de serviços de Internet globalmente em múltiplos pontos de emparelhamento fornecendo mais de 2.000 (Gbps) de capacidade de borda através da rede.

Os roteadores de filtragem na camada de acesso e a borda da rede do Microsoft Azure fornecem segurança bem estabelecida no nível de pacote para impedir tentativas não autorizadas para se conectar ao Azure. Eles garantem que o conteúdo real dos pacotes contenha dados no formato esperado e estejam em conformidade com o esquema de comunicação cliente/servidor esperado. O Azure implementa uma arquitetura em camadas que consiste no seguinte segregação de rede e os componentes de controle de acesso:

- Roteadores de borda - segregam o ambiente de aplicativo da Internet. Os roteadores de borda são projetados para fornecer proteção antifalsificação e limitar o acesso usando Access Control Lists (ACLs).
- Roteadores de distribuição (acesso) - os roteadores de acesso são projetados para permitir somente endereços IP aprovados pela Microsoft, fornecer conexões antifalsificação e estabelecer conexões usando ACLs.

### <a name="a10-ddos-mitigation-architecture"></a>A10 Arquitetura de mitigação de DDOS
Ataques negação de serviço continuam a apresentar realmente uma ameaça à confiabilidade dos serviços online da Microsoft. À medida que os ataques se tornam mais segmentados, mais sofisticados e os serviços da Microsoft se tornam mais geograficamente diversificados, a capacidade de fornecer mecanismos eficazes para identificar e minimizar o impacto desses ataques é uma alta prioridade.

Os detalhes a seguir explicam como o sistema de mitigação de DDOS A10 é implementado de uma perspectiva de arquitetura de rede.  
O Microsoft Azure usa dispositivos de rede A10 em DCR (roteador de Datacenter) que fornecem a mitigação e detecção automatizada. A solução A10 utiliza o monitoramento de rede do Azure para pacotes de rede de fluxo de exemplo e determinar se há um ataque. Depois que o ataque for detectado, A10 dispositivos são usados como scrubbers para mitigar ataques. Após a mitigação, ainda mais limpo tráfego é permitido para o datacenter do Azure diretamente do DCR. A solução A10 é usada para proteger a infraestrutura de rede do Azure.

Proteções contra DDoS na solução A10 incluem:

- Proteção de inundação de UDP IPv4 e IPv6
- Proteção de inundação de ICMP IPv4 e IPv6
- Proteção de inundação de TCP IPv4 e IPv6
- Proteção de ataque de TCP SYN para IPv4 e IPv6
- Ataque de fragmentação

> [!NOTE]
> Proteção contra DDoS é fornecida por padrão para todos os clientes do Azure.
>
>

## <a name="network-connection-rules"></a>Regras de Conexão de Rede
O Azure implanta os roteadores de borda em sua rede que fornecem segurança no nível de pacote para impedir tentativas não autorizadas de se conectarem ao Microsoft Azure. Eles garantem que o conteúdo real dos pacotes contenha dados no formato esperado e estejam em conformidade com o esquema de comunicação cliente/servidor esperado.

Roteadores de borda segregam o ambiente de aplicativo da Internet. Os roteadores de borda são projetados para fornecer proteção antifalsificação e limitar o acesso usando Access Control Lists (ACLs). Esses roteadores de borda são configurados usando uma abordagem de ACL em camadas para limitar os protocolos de rede que podem transitar pelos roteadores de borda e acessar os roteadores.

Os dispositivos de rede são posicionados nos locais de acesso e de borda e atuam como pontos de limite nos quais os filtros de entrada e/ou saída são aplicados.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
