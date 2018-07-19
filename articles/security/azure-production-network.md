---
title: Rede de produção do Azure
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
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101216"
---
# <a name="azure-production-network"></a>Rede de produção do Azure
Os usuários da rede de produção do Azure incluem clientes externos que acessam seus próprios Aplicativos do Microsoft Azure, bem como funcionários internos de suporte do Microsoft Azure que gerenciam a rede de produção. Os métodos de acesso de segurança e os mecanismos de proteção para estabelecer conexões com a rede de produção do Azure são discutidos neste artigo.

## <a name="internet-routing-and-fault-tolerance"></a>Roteamento de Internet e tolerância a falhas
Uma infraestrutura WADNS (Serviço de Nome de Domínio Microsoft Azure) interna e externa redundante e vários clusters de servidores DNS principais e secundários fornecem tolerância a falhas enquanto controles adicionais de segurança de rede do Microsoft Azure, como o NetScaler, são usados para impedir Negação Distribuída de Service (DDoS) e protege a integridade dos serviços DNS do Microsoft Azure.

Os servidores WADNS estão localizados em várias instalações de datacenter. A implementação do WADNS incorpora uma hierarquia de servidores DNS secundários/primários para resolver publicamente os nomes de domínio do cliente do Azure. Os nomes de domínio geralmente são resolvidos para um endereço CloudApp.net, que envolve o endereço IP virtual (VIP) para o serviço do cliente. Exclusivo para o Azure, o VIP correspondente ao endereço IP Dedicado interno (DIP) da tradução do locatário é feito pelos balanceadores de carga da Microsoft responsáveis por esse VIP.

O Azure é hospedado em datacenters do Azure distribuídos geograficamente nos EUA e é construído em plataformas de roteamento de última geração que implementam padrões arquitetônicos robustos e escalonáveis. Estes são alguns dos recursos notáveis:

- Comutação de rótulo multiprotocolo (MPLS) com base em engenharia de tráfego, fornecendo a utilização eficiente de link e redução gradual do serviço, se houver uma interrupção
- As redes são implementadas com "necessidade de mais de um" (N + 1) as arquiteturas de redundância ou melhor.
- Externamente, os datacenters são servidos por circuitos de rede de alta largura de banda dedicados que conectam de forma redundante propriedades com mais de 1.200 provedores de serviços de Internet globalmente em múltiplos pontos de emparelhamento fornecendo mais de 2.000 gigabytes por segundo (Gbps) de capacidade de borda.

Como a Microsoft possui seus próprios circuitos de rede entre datacenters, esses atributos ajudam a oferta do Azure a atingir 99,9% de disponibilidade de rede sem a necessidade de provedores tradicionais de serviços de Internet de terceiros.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Conexão à rede de produção e Firewalls associados
A política de fluxo de tráfego de Internet da rede do Azure direciona o tráfego para a rede de produção do Azure localizado no datacenter regional mais próximo dentro dos Estados Unidos. Como os data centers de Produção do Azure mantêm arquitetura e hardware de rede consistentes, a descrição do fluxo de tráfego abaixo se aplica de maneira consistente a todos os datacenters.

Depois que o tráfego de Internet para o Azure é roteado para o datacenter mais próximo, uma conexão é estabelecida para os roteadores de acesso. Esses roteadores de acesso servem para isolar o tráfego entre nós do Azure e VMs de uma instância do cliente. Dispositivos de infraestrutura de rede nos locais de acesso e de borda são os pontos de limite em que os filtros de entrada e/ou saída são aplicados. Esses roteadores são configurados por meio de uma ACL em camadas para filtrar o tráfego de rede indesejado e aplicar limites de taxa de tráfego, se necessário. O tráfego é permitido por ACL é roteado para os balanceadores de carga. Os roteadores de distribuição são projetados para permitir somente endereços IP aprovados pela Microsoft, fornecer conexões falsas antifalsificação e estabelecidas usando ACLs.

Os dispositivos de Balanceamento de Carga Externo estão localizados atrás dos roteadores de acesso para executar a Conversão de Endereço de Rede (Network Address Translation - NAT) de IPs roteáveis pela Internet para IPs internos do Azure. Eles também encaminham pacotes para IPs e portas internas de produção válidos e atuam como um mecanismo de proteção para limitar a exposição do espaço de endereço interno da Rede de Produção.

Por padrão, a Microsoft aplica HTTPS (Hypertext Transfer Protocol Secure) a todo o tráfego transmitido aos navegadores da Web do cliente, incluindo o login e todo o tráfego posterior. O uso de TLS v1.2 habilita um túnel seguro para o qual o tráfego flua. As ACLs em roteadores de acesso e core Certifique-se que a origem do tráfego é consistente com o que é esperado.

Uma distinção importante nessa arquitetura, em comparação com a arquitetura de segurança tradicional, é que não há firewalls de hardware dedicados, dispositivos especializados de detecção/prevenção de invasão ou outros dispositivos de segurança normalmente esperados antes que as conexões sejam feitas no ambiente de Produção do Azure. Os clientes esperam normalmente esses dispositivos de firewall de hardware na rede do Azure. No entanto, não há nenhum empregado dentro do Azure. Quase exclusivamente, esses recursos de segurança são incorporados ao software que executa o ambiente do Azure para fornecer mecanismos robustos de segurança em várias camadas, incluindo recursos de firewall. Além disso, o escopo do limite e a expansão associada de dispositivos de segurança críticos é mais fácil de gerenciar e inventariar, conforme mostrado na ilustração acima, porque é gerenciado pelo software que executa o Azure.

## <a name="core-security-and-firewall-features"></a>Principais recursos de segurança e firewall
O Azure implementa recursos robustos de segurança e firewall de software em vários níveis para reforçar os recursos de segurança normalmente esperados em um ambiente tradicional para proteger o limite principal de Autorização de Segurança.

### <a name="azure-security-features"></a>Recursos de segurança do Azure
O Azure implementa firewalls de software baseado em host dentro da rede de produção. Várias seguranças principais e recursos de firewall residem dentro do núcleo de ambiente do Azure. Esses recursos de segurança refletem uma estratégia de defesa em profundidade no ambiente do Azure. Dados do cliente no Microsoft Azure são protegidos por firewalls a seguir:

**Firewall de hipervisor (filtro de pacote)**: Este firewall é implementado no hipervisor e configurado por um agente FC. Esse firewall protege o locatário em execução dentro da VM contra acesso não autorizado. Por padrão, quando uma VM é criada, todo o tráfego é bloqueado e o agente FC adicionar regras/exceções no filtro a fim de permitir o tráfego autorizado.

Há duas categorias de regras que são programadas aqui:

- Configuração de máquina ou regras de infraestrutura: por padrão, toda a comunicação é bloqueada. Há exceções para permitir que uma VM envie e receba comunicações do Protocolo de Configuração Dinâmica de Hosts (DHCP), informações de DNS, envie tráfego para a Internet “pública”, de saída para outras VMs no cluster FC e no servidor de Ativação do SO. Como a lista permitida de destinos de saída das VMs não inclui sub-redes de roteadores do Microsoft Azure e outras propriedades da Microsoft, as regras agem como uma camada de defesa para elas.
- Arquivo de Configuração de Função: Define as ACLs de entrada com base no modelo de serviço dos locatários. Por exemplo, se um locatário tiver um front-end da web na porta 80 em uma determinada VM, em seguida, a porta 80 é aberta para todos os endereços IP. Se a VM tiver uma função ou de trabalho em execução, a função de trabalho somente abre para a VM que esteja no mesmo locatário.

**Firewall de Host nativo**: a malha e o armazenamento do Microsoft Azure são executados em um sistema operacional nativo sem hipervisores e, portanto, o firewall do Windows está configurado com os dois conjuntos de regras acima.

**Firewall do host**: o firewall do host existe para proteger a partição do host que executa o hipervisor. As regras são programadas para permitir que as jump boxes conversem com a partição do host em uma porta específica. As outras exceções existem para permitir a resposta DHCP e Respostas DNS. O Azure usa um arquivo de configuração de computador que tem o modelo de regras de firewall para a partição do host. Há também uma exceção de firewall de host que permite que as VMs se comuniquem com os componentes do host, o servidor de rede e o servidor de metadados, por meio de protocolos/portas específicos.

**Firewall convidado**: parte do SO convidado, o que pode ser configurado pelo cliente em VMs de cliente e armazenamento Firewall do Windows.

Recursos de segurança adicionais criados para os recursos do Azure:

- Componentes de infraestrutura são atribuídos endereços IP que são de IPs dedicados (DIPs). Um invasor na Internet não pode resolver o tráfego para esses endereços porque eles não seriam chegam à Microsoft. Roteadores de Gateway de Internet filtrar pacotes endereçados exclusivamente para endereços internos, portanto, não realizaria a rede de produção. Os únicos componentes que aceitam o tráfego direcionado para VIPs são balanceadores de carga.
- Os firewalls implementados em todos os nós internos têm três considerações de arquitetura de segurança principal para qualquer cenário dado:

   - Elas são colocadas por trás de Balanceador de carga (LB) e aceitam pacotes em qualquer lugar. Eles se destinam a ser exposto externamente e correspondem para abrir as portas em um firewall de perímetro tradicional.
   - Aceite somente pacotes de um conjunto limitado de endereços. Isso faz parte da estratégia de defesa aprofundada contra ataques de negação de serviço. Essas conexões são autenticadas criptograficamente.
   - Os firewalls só podem ser acessados a partir de nós internos selecionados. Nesse caso, eles aceitam pacotes apenas de uma lista enumerada de endereços IP de origem, sendo todos DIPs na rede do Azure. Por exemplo, um ataque à rede corporativa poderia direcionar solicitações para esses endereços, mas eles seriam bloqueados, a menos que o endereço de origem do pacote fosse um na lista enumerada na rede do Azure.
   - O roteador de acesso no perímetro bloqueia pacotes de saída endereçados a um endereço que está dentro da rede do Azure devido a suas rotas estáticas configuradas.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
