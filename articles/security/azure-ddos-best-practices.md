---
title: Práticas recomendadas da Proteção contra DDoS do Azure e arquitetura de referência | Microsoft Docs
description: Saiba mais sobre como você pode usar os dados de log para obter informações detalhadas sobre seu aplicativo.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2018
ms.author: barclayn
ms.openlocfilehash: fea235ed057517840515f1ad0543dba1b1c46dd0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Proteção contra DDoS do Azure: práticas recomendadas e arquitetura de referência

Este documento é destinado a tomadores de decisões de TI e à equipe de segurança. Espera-se que você tenha familiaridade com o Azure, rede e segurança.

A criação de resiliência para DDoS (ataque de negação de serviço distribuído) requer planejamento e design para uma variedade de modos de falha. Este documento fornece as práticas recomendadas para a criação de aplicativos no Azure para garantir a resiliência contra ataques de DDoS.

## <a name="types-of-attacks"></a>Tipos de ataques

O DDoS é um tipo de ataque usado em um esforço para esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e a capacidade dele de lidar com solicitações legítimas. Os ataques estão se tornando cada vez mais sofisticados e maiores tanto em tamanho quanto em impacto. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

O Azure fornece proteção contínua contra ataques de DDoS. Essa proteção é integrada à plataforma do Azure por padrão, sem custos adicionais. Além da proteção contra DDoS principal fornecida na plataforma, temos também uma nova oferta denominada “[Proteção contra DDoS do Azure Standard](https://azure.microsoft.com/services/ddos-protection/)”, que fornece recursos avançados de mitigação de DDoS contra ataques de rede, ajustados automaticamente para proteger seus recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de novas redes virtuais. Isso também pode ser feito após a criação inicial e não requer nenhuma alteração de aplicativo ou recurso.

![](media/azure-ddos-best-practices/image1.png)

Ataques de DDoS podem ser classificados amplamente em 3 (três) categorias diferentes

### <a name="volumetric-attacks"></a>Ataques volumétricos

Ataques volumétricos são os tipos mais comuns de ataque de DDoS. Ataques volumétricos utilizam a força bruta contra as camadas de transporte e de rede. Eles tentam esgotar recursos como links de rede. Tais ataques geralmente usam diversos sistemas infectados para inundar as camadas de rede com uma quantidade significativa de tráfego aparentemente legítimo. Protocolos de camada de rede diferentes, como ICMP, UDP e TCP são usados nesse tipo de ataque.

Os ataques de camada de rede de DDoS mais usados são inundação de TCP SYN, eco de ICMP, inundação de UDP, DNS e ataque de amplificação de NTP. Esse tipo de ataque pode ser usado não apenas para interromper o serviço, mas também como um chamariz para invasões de rede mais nefastas e direcionadas. Um exemplo de um ataque volumétrico recente é a [Exploração do memcached](https://www.wired.com/story/github-ddos-memcached/) que afetou o GitHub. Esse ataque foi direcionado à porta UDP 11211 e gerou 1,35 TB/s de volume de ataque.

### <a name="protocol-attacks"></a>Ataques de protocolo

Ataques de protocolo visam protocolos de aplicativo. Eles tentem usar todos os recursos disponíveis em dispositivos de infraestrutura, como firewalls, servidores de aplicativos e balanceadores de carga. Ataques de protocolo usam pacotes mal formados ou que contém anomalias de protocolo. Esses ataques operam enviando um grande número de solicitações abertas, às quais os servidores e outros dispositivos de comunicação respondem e aguardam a resposta do pacote. O destino tenta responder às solicitações abertas, eventualmente fazendo com que o sistema de destino falhe.

O exemplo mais comum de um ataque de DDoS baseado em protocolo é a inundação de TCP SYN. Neste ataque, uma sucessão de solicitações de TCP SYN é direcionada para um destino e pode ser usada para sobrecarregá-lo. A meta é fazer com que o destino pare de responder. A interrupção de Dyn 2016, além de ser um ataque de camada de aplicativo, também consistia em inundações de TCP SYN direcionadas à porta 53 dos servidores DNS do Dyn.

### <a name="resource-attacks"></a>Ataques de recursos

Ataques de recursos visam a camada de aplicativo. Eles disparam processos de back-end buscando sobrecarregar o sistema de destino. Ataques de recursos utilizam indevidamente tráfego que parece normal, mas que traz consultas de uso intensivo de CPU para o servidor. O volume de tráfego necessário para esgotar os recursos é comparativamente menor do que de outros tipos de ataques. Não é possível distinguir o tráfego de um ataque de recursos de um tráfego legítimo, por isso ele é difícil de detectar. Os ataques de recursos mais comuns ocorrem em serviços HTTP/HTTPS e DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade compartilhada na nuvem

Uma estratégia completa de defesa é necessária para combater os cada vez mais variados tipos e a sofisticação dos ataques. A segurança é uma responsabilidade compartilhada entre o cliente e a Microsoft. A Microsoft se refere a isso como um [modelo responsabilidade compartilhada](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). A figura a seguir mostra essa divisão da responsabilidade.

![](media/azure-ddos-best-practices/image2.png)

Clientes do Azure aproveitam os benefícios de ler nossas práticas recomendadas e criar aplicativos distribuídos globalmente projetados e testados contra falhas.

## <a name="fundamental-best-practices"></a>Práticas recomendadas fundamentais

Os ataques de DDoS estão aumentando muito. A seção a seguir fornece diretrizes descritivas para criar serviços resilientes contra DDoS no Azure.

### <a name="design-for-security"></a>Design para segurança

Os clientes devem garantir que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitem que um volume relativamente baixo de solicitações criadas usem uma quantidade excessiva de recursos, resultando em uma interrupção de serviço. Para proteger um serviço em execução no Microsoft Azure, os clientes devem ter uma boa compreensão da arquitetura de seus aplicativos e precisam enfocar os [5 pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars).
Os clientes devem conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

Além disso, é de suma importância garantir que um aplicativo seja resiliente o suficiente para lidar com ataques de negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas diretamente na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

### <a name="design-for-scalability"></a>Design para escalabilidade

Escalabilidade é a capacidade de um sistema de lidar com aumentos de carga. Os clientes precisam projetar seus aplicativos para [escalar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para atender à demanda de carga elevada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias melhora a resiliência e a escalabilidade.

Para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) que oferece várias instâncias. Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../cloud-services/cloud-services-choose-me.md). Para [VMs (Máquinas Virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). É recomendável usar [Conjuntos de Dimensionamento de Máquinas Virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para obter recursos de dimensionamento automático.

### <a name="defense-in-depth"></a>Defesa completa

A ideia por trás da defesa completa é gerenciar riscos com estratégias de defesa diversificadas. Dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de um ataque ser bem-sucedido. É recomendável que os clientes implementem designs seguros para seus aplicativos por meio de recursos internos da plataforma Azure.

Por exemplo, o risco de ataque aumenta conforme o tamanho ou a área da superfície do aplicativo. É recomendável reduzir a área de superfície por meio de lista de permissões para fechar os espaços de endereços IP e as portas ouvintes expostas que não são necessários nos balanceadores de carga ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[Gateway de aplicativo](../application-gateway/application-gateway-create-probe-portal.md)) ou por meio de [NSG (Grupos de Segurança de Rede).](../virtual-network/virtual-networks-nsg.md)
Você pode usar [marcas de serviço](/virtual-network/security-overview.md) e [grupos de segurança de aplicativo](/virtual-network/security-overview.md) para minimizar a complexidade da criação de regra de segurança e configurar a segurança de rede como uma extensão natural da estrutura do aplicativo.

Os clientes devem implantar os serviços do Azure em uma [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Isso permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão. Usar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) alterará o tráfego de serviço para que ele utilize endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, recomendamos que você minimize a exposição dos recursos locais à Internet pública. Use as funcionalidades de escala e de Proteção contra DDoS avançada do Azure implantando suas entidades públicas conhecidas no Azure. Como essas entidades publicamente acessíveis muitas vezes são alvo de ataques de DDoS, colocá-las no Azure reduz o impacto sobre os recursos locais.

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

O Azure tem duas ofertas de serviço de DDoS que fornecem proteção contra ataques de rede (camadas 3 e 4), a Proteção contra ataques DDoS Básica e a Standard. 

### <a name="azure-ddos-basic-protection"></a>Proteção contra DDoS do Azure básica

A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade totais da rede implantada globalmente do Azure fornece defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A Proteção contra DDoS básica não exige nenhuma alteração de configuração ou no aplicativo do usuário. Todos os serviços do Azure, incluindo serviços PaaS, como o DNS do Azure, são protegidos pela Proteção contra DDoS básica.

![](media/azure-ddos-best-practices/image3.png)

A Proteção contra DDoS do Azure básica é consiste em componentes de hardware e de software. Um plano de controle de software decide quando, onde e qual tipo de tráfego deve ser direcionado para dispositivos de hardware que analisam e removem o tráfego de ataque. O plano de controle toma essa decisão com base em uma *política* de Proteção contra DDoS de toda a infraestrutura, que é definida estaticamente e universalmente aplicada a todos os clientes do Azure.

Por exemplo, a política de Proteção contra DDoS especifica em qual volume de tráfego a proteção deve ser *disparada* (ou seja, o tráfego do locatário deve ser roteado para dispositivos de depuração) e, depois, como os dispositivos de depuração devem *mitigar* o ataque.

O serviço básico da Proteção contra DDoS do Azure é voltado para a proteção da infraestrutura e da plataforma do Azure. Ela mitiga o tráfego quando ele excede uma taxa significativa o suficiente para afetar vários clientes em um ambiente multilocatário. Ela não fornece alertas nem políticas personalizadas por cliente.

### <a name="azure-ddos-standard-protection"></a>Proteção contra DDoS do Azure padrão

A proteção padrão fornece recursos avançados de mitigação de DDoS e é automaticamente ajustada para proteger seus recursos específicos do Azure em uma Rede Virtual. É muito simples habilitar a proteção em qualquer Rede Virtual nova ou existente, e ela não exige nenhum aplicativo ou alterações de recursos. Ela tem várias vantagens em comparação com o serviço básico, incluindo registro em log, alertas e telemetria. Estão descritas a seguir as principais diferenças do serviço da Proteção contra DDoS do Azure Padrão.

#### <a name="adaptive-realtime-tuning"></a>Ajuste adaptável em tempo real

O serviço da Proteção contra DDoS do Azure básica ajuda a proteger os clientes, mas somente para impedi-los de afetar outros clientes. Por exemplo, se um serviço está provisionado para um volume típico de tráfego de entrada legítimo menor do que a *taxa de gatilho* da política da proteção contra DDoS de toda a infraestrutura, um ataques de DDoS contra os recursos do cliente em questão pode passar despercebido. Em geral, a natureza complexa dos ataques recentes (por exemplo, DDoS de múltiplos vetores), bem como os comportamentos específicos do aplicativo de locatários necessitam de políticas de proteção personalizadas por cliente.
Isso é realizado usando duas informações:

1. Os padrões de tráfego das camadas 3 e 4 do aprendizado automático por cliente (por IP), e
2. Minimizando falsos positivos, considerando que a escala do Azure permite que ele absorva uma quantidade significativa de tráfego.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>Telemetria, monitoramento e alertas da Proteção contra DDoS

Com a Proteção contra DDoS padrão, expomos uma telemetria sofisticada por meio do [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) por toda a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado ao Splunk (Hubs de Eventos do Azure), o Azure Log Analytics e o Armazenamento do Microsoft Azure para obter análise avançada por meio da interface de Diagnóstico do Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação de DDoS

No Portal do Azure, clique em **Monitorar** \> **Métricas**. Na tela **Métricas**, selecione o grupo de recursos, o tipo de recurso do Endereço IP Público e o endereço IP público do Azure. Métricas de DDoS ficarão visíveis no painel de métricas disponíveis.

A Proteção contra DDoS padrão aplica **TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente)** para cada IP público do recurso protegido, na VNET que tem o DDoS habilitado. Exiba os limites da política selecionando a métrica **“Pacotes de entrada para disparar a mitigação de DDoS“**.

![](media/azure-ddos-best-practices/image7.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina. A mitigação de DDoS ocorre para um endereço IP sob ataque somente quando o limite da política for excedido.

##### <a name="under-ddos-attack"></a>Sob ataque de DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica “sob ataque de DDoS ou não” mudará para 1 conforme executamos a mitigação do tráfego do ataque.

![](media/azure-ddos-best-practices/image8.png)

É recomendável configurar um alerta nessa métrica, para ser notificado quando houver uma mitigação de DDoS ativa executada no seu endereço IP público.

Para ver mais informações, consulte [Gerenciar a Proteção contra DDoS do Azure padrão usando o Portal do Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Ataques de recursos

Especificamente para ataques de recursos na camada de aplicativo, os clientes devem configurar o WAF (Firewall do Aplicativo Web) para ajudar a proteger os aplicativos Web. O WAF inspeciona o tráfego de entrada da Web para bloquear injeções de SQL, scripts intersites, DDoS e outros ataques da camada 7. O Azure fornece o [WAF como um recurso do Gateway de Aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md) para proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. Há diversas ofertas de WAF disponíveis de parceiros do Azure que podem ser mais adequadas para suas necessidades no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Mesmo os Firewalls do Aplicativo Web estão suscetíveis a ataques de esgotamento volumétrico e de estado. É altamente recomendável ativar Proteção contra DDoS padrão em uma Rede Virtual WAF para protegê-la contra ataques volumétricos e de protocolo. Para obter mais informações, consulte a seção de arquitetura de referência.

### <a name="protection-planning"></a>Planejamento de proteção

Planejamento e preparação são cruciais para entender como será o desempenho de um sistema durante um ataque de DDoS. Este planejamento e preparação também ajudam a elaborar um plano de resposta de gerenciamento de incidentes.

Os clientes devem garantir que a Proteção contra DDoS padrão esteja habilitada na rede virtual dos pontos de extremidade voltados para a Internet. Configurar alertas de DDoS ajuda a manter um olhar atento e constante sobre qualquer possível ataque na sua infraestrutura. Os clientes devem monitorar seus aplicativos independentemente. Eles precisam entender o comportamento normal do aplicativo. Será necessário tomar medidas caso o aplicativo não esteja se comportando conforme o esperado durante um ataque de DDoS.

#### <a name="ddos-attacks-orchestration"></a>Orquestração de ataques de DDoS

É uma prática recomendada testar suas suposições sobre como os serviços respondem a um ataque, mesmo antes que ele ocorra, conduzindo simulações periódicas. Durante o teste, valide se os serviços ou aplicativos continuam a funcionar conforme esperado e não há interrupções na experiência do usuário final. Identifique lacunas do ponto de vista de tecnologia e de processo, e incorpore a estratégia de resposta de DDoS. Uma recomendação geral é realizar esses testes em ambientes de preparo ou fora do horário de pico para minimizar o impacto no ambiente de produção.

Firmamos parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que os clientes do Azure podem gerar tráfego contra os pontos de extremidade públicos com Proteção contra DDoS habilitada para fins de simulação. A simulação do [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) permitirá que você:

- Valide como a Proteção contra DDoS do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS

- Otimize o processo de resposta a incidentes durante ataques de DDoS

- Documente a conformidade de DDoS

- Treine suas equipes de segurança de rede

A segurança cibernética é uma batalha implacável que exige constante inovação na proteção. A Proteção contra DDoS Standard do Azure é uma oferta de ponta tstate-of-the-artstomers com uma solução efetiva para mitigar ataques de DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Na maioria dos casos que um ataque de DDoS visa seus recursos do Azure, não há quase nenhuma intervenção necessária do ponto de vista do usuário final. Ainda assim, incorporar a mitigação de DDoS como parte da estratégia de resposta a incidentes da organização garantirá um impacto mínimo na continuidade de negócios.

### <a name="microsoft-threat-intelligence"></a>Inteligência contra ameaças da Microsoft

A Microsoft conta com uma abrangente rede de inteligência contra ameaças que usa o conhecimento coletivo de uma comunidade de segurança estendida que dá suporte aos serviços online da Microsoft, parceiros da Microsoft e relações da comunidade de segurança da Internet. Como um provedor de infraestrutura crítica, a Microsoft recebe avisos antecipados sobre ameaças, obtém informações de inteligência contra ameaças de outros serviços online e de sua base de clientes globais da Microsoft. Toda a inteligência contra ameaças da Microsoft é reincorporada em seus produtos de Proteção contra DDoS do Azure.

Além disso, a DCU (Unidade de Crimes Digitais) da Microsoft executa estratégias ofensivas contra botnets, uma fonte comum de comando e controle de ataques de DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Realize uma avaliação de risco dos seus recursos do Azure

É fundamental compreender o escopo do seu risco a ataques de DDoS continuamente. Os clientes devem perguntar periodicamente: quais novos recursos do Azure disponíveis publicamente precisam de proteção? Há um ponto único de falha no serviço? Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizá-los para os clientes válidos? Há redes virtuais em que a Proteção contra DDoS padrão deve ser habilitada, mas ainda não está? Meu serviços são ativo/ativo com failover em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipe de resposta a DDoS do cliente

Criar uma equipe de resposta a DDoS é uma etapa importante para garantir a resposta rápida e eficiente a um ataque. Os clientes precisam identificar vários contatos na organização que supervisionarão o planejamento e a execução. A Equipe de Resposta a DDoS deve conhecer profundamente o serviço da Proteção contra DDoS do Azure padrão, bem como estar apta a identificar e reduzir um ataque para coordenar-se com diversos clientes internos e externos, incluindo a equipe do Suporte da Microsoft, se necessário.

A Microsoft recomenda incorporar exercícios de planejamento e simulação à Equipe de Resposta a DDoS, incluindo testes de escala, como parte normal do planejamento de continuidade e disponibilidade do serviço. 

### <a name="during-an-attack"></a>Durante um ataque

A Proteção contra DDoS do Azure padrão identificará e mitigará os ataques de DDoS sem qualquer intervenção do usuário. Para ser notificado quando houver uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](../virtual-network/ddos-protection-manage-portal.md) na métrica “sob ataque de DDoS ou não”. Você pode ainda examinar e criar alertas conforme desejado para as outras métricas de DDoS para entender a escala do ataque, o tráfego que está sendo descartado, etc.

#### <a name="when-to-contact-microsoft-support"></a>Quanto entrar em contato com o Suporte da Microsoft

- Se, durante um ataque de DDoS, você descobrir que o desempenho do recurso protegido foi drasticamente prejudicado ou se o recurso não estiver disponível.

- Se você achar que o serviço da Proteção contra DDoS não está se comportando conforme o esperado. O serviço de Proteção contra DDoS iniciará a mitigação apenas se os critérios abaixo forem atendidos:

    - O valor da métrica “Política para disparar a mitigação de DDoS” (TCP/TCP SYN/UDP) é menor do que o tráfego recebido no recurso de IP público protegido.

- Se você souber que vai receber um evento viral planejado que causará um aumento significativo no seu tráfego de rede.

- Se um ator ameaçou iniciar um ataque de DDoS contra seus recursos.

Para problemas que afetam o negócio de maneira crítica, crie um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) com gravidade A.

### <a name="post-attack-steps"></a>Etapas pós-ataque

Sempre é uma boa estratégia realizar uma análise posterior ao ataque e reajustar a estratégia de resposta a DDoS conforme necessário. Itens a serem considerados:

- Ocorreu alguma interrupção no serviço ou na experiência do usuário final devido à falta de arquitetura escalonável?

- Quais aplicativos ou serviços mais sofreram?

- A estratégia de resposta a DDoS foi efetiva e como ela pode ser melhorada ainda mais?

Se você suspeitar que está sofrendo em um ataque de DDoS, escale a questão por meio dos canais de suporte normais do Azure.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência da Proteção contra DDoS

A Proteção contra DDoS padrão destina-se [a serviços que são implantados em uma Rede Virtual.](../virtual-network/virtual-network-for-azure-services.md) Para outros serviços, a Proteção contra DDoS Básica padrão será aplicada. As arquiteturas de referência específicas descritas abaixo são organizadas por cenários, com padrões de arquitetura agrupados.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicativo em execução em VMs com balanceamento de carga

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para executar várias VMs (máquinas virtuais) do Windows em um conjunto de dimensionamento atrás de um balanceador de carga para melhorar a disponibilidade e a escalabilidade. Essa arquitetura pode ser usada para qualquer carga de trabalho sem estado, como um servidor Web.

![](media/azure-ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída em várias instâncias de VM. Há um único endereço IP público e o tráfego da Internet é distribuído para as VMs usando um balanceador de carga. A Proteção contra DDoS padrão está habilitada na Rede Virtual do balanceador de carga do Azure (Internet) que tem o IP público associado a ela.

O balanceador de carga distribui as solicitações de entrada da Internet para as instâncias de VM. Os Conjuntos de Dimensionamento de VMs permitem que o número de VMs seja reduzido ou aumentado horizontalmente manualmente, ou automaticamente com base em regras predefinidas. Isso é importante se o recurso está sob ataque de DDoS. Consulte este [artigo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) para obter mais informações sobre esta arquitetura de referência.

#### <a name="applications-running-on-windows-n-tier"></a>Aplicativos em execução em N camadas do Windows

Há muitas maneiras de implementar uma arquitetura de N camadas. O diagrama mostra um aplicativo Web de três camadas típico. Essa arquitetura se baseia em [Executar VMs com balanceamento de carga para escalabilidade e disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). As camadas comerciais e da Web usam VMs com balanceamento de carga.

![](media/azure-ddos-best-practices/image10.png)

Na arquitetura acima, a Proteção contra DDoS padrão está habilitada na Rede Virtual. Todos os IPs públicos na Rede Virtual terão Proteção contra DDoS nas camadas 3 e 4. Para proteção da camada 7, implante o Gateway de Aplicativo no SKU do WAF. Consulte [este](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) artigo para obter mais informações sobre esta arquitetura de referência.

#### <a name="paas-web-application"></a>Aplicativo Web PaaS

Essa arquitetura de referência mostra a execução de um aplicativo do Serviço de Aplicativo do Azure em uma única região. Essa arquitetura mostra um conjunto de práticas comprovadas para um aplicativo Web que usa o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/services/app-service/) e o [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/).
A região em espera é configurada para cenários de failover.

![](media/azure-ddos-best-practices/image11.png)

O Gerenciador de Tráfego roteia as solicitações de entrada para o Gateway de Aplicativo em uma das regiões. Durante as operações normais, ele roteia as solicitações para o Gateway de Aplicativo na região ativa. Se essa região ficar não disponível, o Gerenciador de Tráfego fará failover para o Gateway de Aplicativo na região Em espera.

Todo o tráfego da Internet destinado ao aplicativo Web é roteado para o [Endereço IP público do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) por meio do Gerenciador de Tráfego. Nesse cenário, o Serviço de Aplicativo (aplicativos Web) em si não é diretamente externo e é protegido pelo Gateway de Aplicativo. É recomendável configurar o SKU do WAF do Gateway de Aplicativo (modo de prevenção) para proteger contra ataques da camada 7 (HTTP/HTTPS/Soquete Web). Além disso, os aplicativos Web são configurados para [aceitar o tráfego somente do endereço IP do Gateway de Aplicativo](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Consulte [este](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) artigo para obter mais informações sobre esta arquitetura de referência.

### <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços PaaS não Web

#### <a name="hdinsight-on-azure"></a>HDInsight no Azure

Essa arquitetura de referência mostra a configuração da Proteção contra DDoS padrão para [cluster de HDInsight](https://docs.microsoft.com/azure/hdinsight/) no Azure. É necessário verificar se o cluster do HDInsight está vinculado a uma rede virtual e a Proteção contra DDoS está habilitada nessa Rede Virtual.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

Nesta arquitetura, o tráfego destinado ao cluster de HDInsight vindo da Internet é roteado para o IP público associado ao balanceador de carga de gateway do HDInsight. O balanceador de carga de gateway, em seguida, envia o tráfego para os nós principais ou nós de trabalho diretamente. Considerando que a Proteção contra DDoS Standard esteja habilitada na Rede Virtual do HDInsight, todos os IPs públicos na Rede Virtual receberão proteção contra DDoS nas camadas 3 e 4. A arquitetura de referência acima pode ser combinada com arquiteturas de referência de N camadas/multirregião.

Para obter mais detalhes sobre a arquitetura de referência, consulte a documentação [Estender o Azure HDInsight usando uma Rede Virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-api-management"></a>Gerenciamento de API do Azure

Essa arquitetura de referência protege o ponto de extremidade público do recurso de [gerenciamento de API](../api-management/api-management-key-concepts.md) que publica APIs para clientes externos para a organização. O APIM deve ser implantado em uma Rede Virtual externa para habilitar a Proteção contra DDoS.

![](media/azure-ddos-best-practices/image15.png)

Ao configurar a Rede Virtual, o gateway de Gerenciamento de API e o Portal do Desenvolvedor poderão ser acessados pela Internet pública por meio de um balanceador de carga público. Nesta arquitetura, a Proteção contra DDoS padrão está habilitada na Rede Virtual externa do APIM. O tráfego é roteado da Internet para o endereço IP público do APIM, que está protegido contra ataques de rede das camadas 3 e 4. Para proteger contra ataques HTTP/HTTPS da camada 7, configure um Gateway de Aplicativo no modo de WAF.

A lista dos serviços adicionais que são implantados em uma Rede Virtual e podem ser configurados para a Proteção contra DDoS padrão é mantida [aqui](../virtual-network/virtual-network-for-azure-services.md). A Proteção contra DDoS padrão é compatível somente com recursos Azure Resource Manager. *A implantação de ASE (Ambiente do Serviço de Aplicativo) injetado em uma VNET com um IP público não tem suporte nativo.* Para obter detalhes sobre como proteger o ambiente ASE, consulte esta seção.

## <a name="next-steps"></a>Próximas etapas

* [Página de produto da Proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/)

* [Blog da Proteção contra DDoS do Azure](http://aka.ms/ddosblog)

* [Documentação da Proteção contra DDoS do Azure ](../virtual-network/ddos-protection-overview.md)
