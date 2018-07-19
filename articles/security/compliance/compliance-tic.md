---
title: Diretrizes de Conexão Confiável com a Internet para o Azure
description: Diretrizes de Conexão Confiável com a Internet para o Azure e serviços SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: cf24810c0aa414e751e55df163563f013c1a0081
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969938"
---
# <a name="trusted-internet-connection-guidance"></a>Diretrizes de Conexão Confiável com a Internet

## <a name="background"></a>Segundo plano

A finalidade da Iniciativa TIC (Conexões Confiáveis com a Internet) é otimizar e padronizar a segurança das conexões de rede externas individuais atualmente em uso por agências federais. A política é descrita no [memorando M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) do OMB (Departamento de Gerenciamento e Orçamento).

Em novembro de 2007, o OMB estabeleceu o programa TIC para melhorar a segurança de perímetro de rede federal e as funções de resposta a incidentes. O TIC foi projetado para executar a análise de rede de todo o tráfego .gov de entrada e saída para identificar assinaturas e dados baseados em padrão específicos e revelar anomalias comportamentais, como a atividade de botnet. As agências foram obrigadas a consolidar suas conexões de rede externas e encaminhar todo o tráfego por meio de dispositivos de detecção e prevenção contra invasões (conhecidos como EINSTEIN) hospedados em um número limitado de pontos de extremidade de rede (chamados de Conexões Confiáveis com a Internet).

Em outras palavras, o objetivo do TIC é que as agências saibam:
- Quem está em minha rede (autorizado ou não autorizado)?
- Quando minha rede é acessada e por quê?
- Quais recursos estão sendo acessados?

Hoje, todas as conexões externas das agências precisam ser encaminhadas por meio de um TIC aprovado pelo OMB. As agências federais devem participar do programa TIC como um TICAP (Provedor de Acesso TIC) ou contratando serviços de um dos principais Provedores de Serviços de Internet da Camada 1, conhecidos como provedores MTIPS (Serviço de Protocolo IP Confiável Gerenciado).  O TIC inclui funcionalidades críticas obrigatórias que são executadas hoje pela agência e pelo provedor MTIPS. Na versão atual do TIC, os dispositivos de detecção de invasões EINSTEIN versão 2 e de prevenção de invasões EINSTEIN versão 3 acelerados (3A) são implantados em cada TICAP e MTIPS, e a agência estabelece um Memorando de Entendimento com o DHS (Departamento de Segurança Nacional) para implantar as funcionalidades EINSTEIN em sistemas federais.

Como parte de sua responsabilidade em proteger a rede .gov, o DHS exige que os feeds de dados brutos dos dados de Netflow da agência correlacionem os incidentes em toda a empresa federal e realize análises usando ferramentas especializadas. Os roteadores do DHS fornecem a capacidade de coletar o tráfego de rede IP conforme ele entra ou sai de uma interface. Analisando os dados de fluxo de rede, um administrador de rede pode determinar aspectos como a origem e o destino do tráfego, a classe de serviço, etc. Os dados de fluxo de rede são considerados "dados sem conteúdo" (por exemplo, cabeçalho, IP de origem, IP de destino, etc.) e permitem que o DHS saiba informações sobre o conteúdo; ou seja, quem estava fazendo o que e por quanto tempo.

A iniciativa também inclui políticas de segurança, diretrizes e estruturas que supõem a infraestrutura local. À medida que as agências governamentais migram para a nuvem para obter economia de custo, eficiência operacional e inovação, os requisitos de implementação do TIC, em alguns casos, reduzem o tráfego de rede e limitam a velocidade e a agilidade com as quais os usuários governamentais podem acessar seus dados baseados em nuvem.

Este artigo aborda como as agências governamentais podem usar o Microsoft Azure Governamental para ajudar a alcançar a conformidade com a política do TIC em todos os serviços de IaaS e PaaS.

## <a name="summary-of-azure-networking-options"></a>Resumo das opções de rede do Azure

Há três opções principais ao se conectar aos serviços do Azure:

1. Conexão direta com a Internet: conecte-se aos serviços do Azure diretamente por meio de uma conexão aberta com a Internet. O meio é público, bem como a conexão. As criptografias no nível do aplicativo e do transporte são utilizadas para garantir a privacidade. A largura de banda é limitada pela conectividade com a Internet de um site e vários provedores ativos podem ser usados para garantir a resiliência
1. Rede Virtual Privada: conecte-se à Rede Virtual do Azure com privacidade usando um Gateway de VPN.
O meio é público, pois ele atravessa a conexão padrão com a Internet de um site, mas a conexão é criptografada em um túnel para garantir a privacidade. A largura de banda é limitada, dependendo dos dispositivos VPN e da configuração escolhida. As conexões Ponto a Site do Azure são normalmente limitadas a 100 Mbps, enquanto as conexões Site a Site são limitadas a 1,25 Gbps.
1. Microsoft ExpressRoute: o ExpressRoute é uma conexão direta aos serviços da Microsoft. Como a conectividade é estabelecida por um canal de fibra isolado, a conexão pode ser pública ou privada, dependendo da configuração usada. A largura de banda geralmente é limitada a um máximo de 10 Gbps.

Há várias maneiras de atender aos requisitos do Apêndice H (Considerações sobre a nuvem) da Conexão Confiável com a Internet, encontrado no "Documento da Arquitetura de Referência do TIC (Conexões Confiáveis com a Internet), versão 2.0" do Departamento de Segurança Nacional. Neste documento, as diretrizes do DHS para o TIC serão designadas TIC 2.0.

Para permitir a conexão do Departamento ou da Agência (D/A) com o Azure ou o Office 365, sem o tráfego de roteamento por meio do TIC do D/A, o D/A precisa usar um túnel criptografado e/ou uma conexão dedicada com o CSP (Provedor de Serviços de Nuvem). Os serviços do CSP podem garantir que a conectividade com os ativos de nuvem do D/A e que eles não sejam oferecidos para a Internet pública para o acesso de funcionários diretos da agência.

O O365 está em conformidade com o TIC 2.0 – Apêndice H usando o ExpressRoute com o [Emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) habilitado ou uma conexão com a Internet que criptografa todo o tráfego usando o TLS 1.2.  Os usuários finais de D/A na rede de D/A podem se conectar por meio da rede e da infraestrutura de TIC de sua agência pela Internet. Todo o acesso remoto à Internet ao O365 é bloqueado e encaminhado por meio da agência. O D/R também pode se conectar ao O365 por uma conexão do ExpressRoute com o emparelhamento da Microsoft, que é um tipo de Emparelhamento Público, habilitado.  

Somente para o Azure, as opções 2 (VPN) e 3 (ExpressRoute) podem atender a esses requisitos quando elas são usadas em conjunto com serviços que limitam o acesso à Internet.

![Diagrama do TIC (Conexão Confiável com a Internet) da Microsoft](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Como as ofertas de Infraestrutura como Serviço do Azure podem ajudar na conformidade do TIC

A conformidade com a política do TIC usando o IaaS é relativamente simples, já que os clientes do Azure gerenciam seu próprio roteamento de rede virtual.

O principal requisito para ajudar a garantir a conformidade com a arquitetura de referência do TIC é garantir que a VNET (Rede Virtual) se torne uma extensão particular da rede do Departamento ou da Agência. Para se tornar uma extensão _particular_, a política exige que nenhum tráfego saia de sua rede, exceto por meio da conexão de rede TIC local. Esse processo é conhecido como "Túnel Forçado", que, quando usado para a conformidade do TIC, é o processo de roteamento de todo o tráfego de qualquer sistema no ambiente do CSP para que ele passe por um gateway local na rede de uma organização para a Internet por meio do TIC.

A conformidade do TIC no IaaS do Azure pode ser dividida em duas etapas principais:

1. Configuração
1. Auditoria

### <a name="azure-iaas-tic-compliance-configuration"></a>Configuração de conformidade do TIC no IaaS do Azure

Para configurar uma arquitetura em conformidade com o TIC com o Azure, você precisa primeiro impedir o acesso direto à Internet à sua rede virtual e, em seguida, forçar o tráfego da Internet por meio da rede local.

#### <a name="prevent-direct-internet-access"></a>Impedir o acesso direto à Internet

A rede de IaaS do Azure é realizada por meio de Redes Virtuais, compostas por Sub-redes, às quais as NICs (Controladores de Adaptador de Rede) das Máquinas Virtuais estão associadas.

O cenário mais simples para dar suporte à conformidade do TIC é garantir que uma Máquina Virtual, ou uma coleção dela, não possa se conectar aos recursos externos. A desconexão de redes externas pode ser garantida com o uso de NSGs (Grupos de Segurança de Rede), que podem ser usados para controlar o tráfego para uma ou mais NICs ou sub-redes na rede virtual. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego com base na direção do tráfego, no protocolo, no endereço e porta de origem e no endereço e porta de destino. As regras de um NSG podem ser alteradas a qualquer momento e as alterações se aplicam a todas as instâncias associadas.  Para saber mais sobre como criar um NSG, confira o artigo [Criando um NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Forçar o tráfego da Internet por meio da rede local

O Azure cria rotas de sistema automaticamente e as atribui a cada sub-rede em uma rede virtual. Não é possível criar nem remover rotas de sistema, mas é possível substituir algumas rotas de sistema por rotas personalizadas. O Azure cria rotas de sistema padrão para cada sub-rede e adiciona rotas padrão opcionais a sub-redes específicas ou a todas as sub-redes quando você usa as funcionalidades específicas do Azure. Esse roteamento garante que o tráfego destinado na Rede Virtual permaneça na Rede Virtual, que os espaços de endereço privado designados pela IANA como 10.0.0.0/8 sejam removidos (a menos que sejam incluídos no espaço de endereço da Rede Virtual) e o roteamento como “último recurso” de 0.0.0.0/0 para o ponto de extremidade da Internet da Rede Virtual.

![Túnel forçado do TIC](media/tic-diagram-c.png)

Para garantir que todo o tráfego atravesse o TIC do D/A, todo o tráfego que sai da Rede Virtual precisa ser roteado por meio da conexão Local.  Crie rotas personalizadas criando rotas definidas pelo usuário ou trocando rotas do BGP (Border Gateway Protocol) entre o gateway de rede local e um gateway de rede virtual do Azure. Encontre mais informações sobre as Rotas definidas pelo usuário em https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Encontre também mais informações sobre o Border Gateway Protocol em https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Rotas definidas pelo usuário

Se você estiver usando um Gateway de Rede Virtual baseado em rota, o túnel forçado poderá ser feito no Azure, com a adição de uma UDR (Rota Definida pelo Usuário) definindo que o tráfego de 0.0.0.0/0 seja roteado para um "Próximo Salto" do Gateway de Rede Virtual. O Azure prioriza as Rotas Definidas pelo Usuário em relação às Rotas Definidas pelo Sistema e, portanto, isso resulta no envio de todo o tráfego não VNET para o Gateway de Rede Virtual, que pode então encaminhá-lo para o Local. Depois de definida, essa Rota Definida pelo Usuário precisa ser associada a todas as Sub-redes existentes ou recém-criadas em todas as Redes Virtuais no Ambiente do Azure.

![Rotas definidas pelo usuário e TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Se você estiver usando um Gateway de Rede Virtual habilitado para o ExpressRoute ou para um BGP (Border Gateway Protocol), o BGP será o mecanismo preferencial para anunciar as rotas. Com uma rota anunciada pelo BGP igual a 0.0.0.0/0, os Gateways de Rede Virtual com reconhecimento do ExpressRoute e do BGP garantirão que essa rota padrão seja aplicada a todas as Sub-redes nas Redes Virtuais.

### <a name="azure-iaas-tic-compliance-auditing"></a>Auditoria de conformidade do TIC no IaaS do Azure

O Azure oferece várias maneiras de auditar a conformidade do TIC.

#### <a name="effective-routes"></a>Rotas Efetivas

Para confirmar se a rota padrão foi propagada, observe as "Rotas Efetivas" de determinada VM, NIC ou Tabela de Rotas Definidas pelo Usuário. Isso pode ser feito por meio do portal do Azure, conforme descrito em https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, ou por meio do PowerShell, conforme descrito em https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. A folha Rotas Efetivas permitirá que você veja as Rotas Definidas pelo Usuário relevantes, as rotas anunciadas pelo BGP e as rotas do Sistema que se aplicam à entidade relevante, conforme visto abaixo.

![captura de tela das rotas](media/tic-screen-1.png)

**Observação**: não é possível exibir as rotas efetivas para uma NIC, a menos que ela esteja associada a uma VM em execução.

#### <a name="network-watcher"></a>Observador de Rede

O Observador de Rede do Azure oferece várias ferramentas que podem ser usadas para auditar a conformidade do TIC.  Saiba mais sobre o Observador de Rede em https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Logs de fluxo dos Grupos de Segurança de Rede 

O Observador de Rede do Azure fornece a capacidade de capturar logs de fluxo de rede que indicam os metadados em torno do tráfego de IP. Além de outros dados, os logs de fluxo de rede contêm os endereços de origem e de destino dos destinos. Isso, combinado com os logs do Gateway de Rede Virtual, dos dispositivos de borda Locais e/ou do TIC, permitirá o monitoramento de que todo o tráfego está, de fato, sendo roteado para o Local. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Como as ofertas de Plataforma como Serviço do Azure podem ajudar na conformidade do TIC

Os serviços de PaaS do Azure como o Armazenamento do Azure podem ser acessados por uma URL acessível pela Internet. Qualquer pessoa com credenciais aprovadas pode acessar o recurso, como uma conta de armazenamento, em qualquer local sem atravessar o TIC. Por esse motivo, muitos clientes governamentais concluem incorretamente que os serviços de PaaS do Azure não estão em conformidade com as políticas TIC. Na verdade, muitos serviços de PaaS do Azure podem estar em conformidade com a política do TIC usando a mesma arquitetura como um ambiente de IaaS em conformidade com o TIC descrito acima, caso eles possam ser anexados a uma VNET (Rede Virtual). A integração dos serviços de PaaS do Azure com uma VNET do Azure permite que o serviço esteja acessível em modo particular nessa VNET e permite que o roteamento personalizado para 0.0.0.0/0 seja aplicado por meio de Rotas Definidas pelo Usuário ou do BGP, garantindo que todo o tráfego direcionado à Internet seja roteado para o Local para atravessar o TIC.  Alguns serviços do Azure podem ser integrados em VNETs usando os seguintes padrões:

- **Implantar uma instância dedicada do serviço**: um número cada vez maior de serviços de PaaS pode ser implantado como instâncias dedicadas com pontos de extremidade anexados à VNET. Por exemplo, um ASE (Ambiente de Serviço de Aplicativo) pode ser implantado no modo “Isolado”, permitindo que seu ponto de extremidade de rede fique restrito a uma VNET. Em seguida, esse ASE pode hospedar muitos serviços de PaaS do Azure, como Aplicativos Web, APIs e Functions.
- **Pontos de Extremidade de Serviço da VNET**: um número cada vez maior de serviços de PaaS permitem a opção de mover seu ponto de extremidade para um IP privado da VNET, em vez de para um endereço público.

Os serviços que dão suporte à implantação de instâncias dedicadas em uma VNET ou em Pontos de Extremidade de Serviço a partir de maio de 2018 são listados abaixo: *(A disponibilidade representa o Azure Comercial; a disponibilidade no Azure Governamental está pendente).

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

|Serviço                   |Status            |
|--------------------------|------------------|
|Azure KeyVault            | Visualização particular  |
|Cosmos DB                 | Visualização particular  |
|Identidade                  | Visualização particular  |
|Azure Data Lake           | Visualização particular  |
|SQL Postgres/Mysql       | Visualização particular  |
|SQL Data Warehouse do Azure  | Visualização pública   |
|SQL do Azure                 | GA               |
|Armazenamento                   | GA               |

### <a name="vnet-injection"></a>Injeção de VNET

|Serviço                            |Status            |
|-----------------------------------|------------------|
|Instância Gerenciada do SQL               | Visualização pública   |
|AKS (Serviço de Contêiner do Azure)       | Visualização pública   |
|Service Fabric                     | GA               |
|Gerenciamento de API                     | GA               |
|Azure Active Directory             | GA               |
|Lote do Azure                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|Conjunto de dimensionamento de máquinas virtuais de computação  | GA               |
|Serviço de Nuvem de Computação              | GA               |

### <a name="vnet-integration-details"></a>Detalhes da Integração VNET

O diagrama abaixo explica o fluxo de rede geral para acesso aos serviços de PaaS usando a Injeção de VNET e o Túnel de Serviço de VNET.  Encontre mais informações sobre Gateways de Serviço de Rede, VNETs e Marcas de Serviço aqui https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Opções de conectividade de PaaS para o TIC](media/tic-diagram-e.png)

1. Conexão particular ao Azure usando o ExpressRoute. O emparelhamento privado do ExpressRoute com túnel forçado é usado para forçar todo o tráfego da VNET do Cliente pelo ExpressRoute novamente para o local. O Emparelhamento da Microsoft não é necessário.
2. O Gateway de VPN do Azure usado em conjunto com o emparelhamento do Microsoft ExpressRoute pode ser usado para sobrepor a criptografia de IPSec de ponta a ponta entre a VNET do Cliente e a borda local. 
3. A conectividade de rede com a VNET do Cliente é controlada usando NSG (Grupos de Segurança de Rede) permitindo que os clientes concedam permissões/negações com base no IP, na porta e no protocolo.
4. A VNET do Cliente é estendida para o serviço de PaaS, criando um Ponto de Extremidade de Serviço para o serviço do cliente.
5. O Ponto de Extremidade de Serviço de PaaS é protegido para negar tudo por padrão e permitir o acesso somente de sub-redes especificadas dentro da VNET do Cliente.  A negação padrão também inclui conexões provenientes da Internet.
6. Outros serviços do Azure que precisam acessar recursos na VNET do Cliente devem ser:  
  a. Implantado diretamente na VNET  
  b. Permitido seletivamente com base nas diretrizes do respectivo serviço do Azure.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Opção 1: "injeção de VNET" de instância dedicada

Com a Injeção de VNET, os clientes podem implantar seletivamente instâncias dedicadas de determinado serviço do Azure, como o HDInsight, em sua própria VNET. Instâncias de serviço são implantadas em uma sub-rede dedicada na VNet de um cliente. A injeção de VNET permite que os recursos de serviço sejam acessíveis por meio de endereços não roteáveis para a Internet.  As instâncias locais podem acessar essas instâncias de serviço por meio do espaço de endereço de VNET diretamente por meio do ExpressRoute ou da VPN Site a Site, em vez de abrir firewalls no espaço de endereço da Internet pública. Com uma instância dedicada anexada a um ponto de extremidade, as mesmas estratégias usadas para conformidade do TIC de IaaS podem ser utilizadas, com o roteamento padrão garantindo que o tráfego direcionado para a Internet seja redirecionado para um Gateway de Rede Virtual direcionado para o Local. O acesso de entrada e saída pode ser controlado ainda mais por meio de NSGs (Grupos de Segurança de Rede) para a sub-rede especificada.

![Diagrama de visão geral da injeção de VNET](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Opção 2: Pontos de Extremidade de Serviço de VNET 

Um número cada vez maior de serviços de multilocatário do Azure oferece a funcionalidade “Ponto de Extremidade de Serviço”, um método alternativo para integração a VNETs do Azure. Os Pontos de Extremidade de Serviço da VNET estendem o espaço de endereço IP da VNET e a identidade da VNET para o serviço por meio de uma conexão direta.  O tráfego da VNET para o serviço do Azure sempre permanece dentro da rede de backbone do Azure. Depois que um Ponto de Extremidade de Serviço é habilitado para um serviço, as conexões com o serviço podem ser restritas a essa VNET por meio de políticas expostas pelo serviço. As verificações de acesso são impostas na plataforma pelo Serviço do Azure, e o acesso ao recurso bloqueado é concedido somente se a solicitação se origina na VNET/Sub-rede permitida e/ou nos dois IPs usados para identificar o tráfego OnPremises, caso você use o ExpressRoute. Isso pode ser usado para impedir efetivamente que o tráfego de Entrada/Saída saia diretamente do Serviço de PaaS.

![Diagrama de visão geral dos Pontos de Extremidade de Serviço](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Usando ferramentas nativas de nuvem para o reconhecimento situacional de rede

O Azure fornece ferramentas nativas de nuvem para ajudar a garantir que você tenha o reconhecimento situacional necessário para entender os fluxos de tráfego da rede. Elas não são necessárias para alcançar a conformidade com a política do TIC, mas podem melhorar muito as funcionalidades de segurança.

### <a name="azure-policy"></a>Azure Policy

A Política do Azure (https://azure.microsoft.com/services/azure-policy/) é um serviço do Azure que oferece à sua organização uma melhor capacidade de auditar e impor as iniciativas de conformidade.  Atualmente disponível em Versão Prévia Pública nas Nuvens Comerciais do Azure, mas ainda não no Microsoft Azure Governamental, os clientes preocupados com o TIC podem começar a planejar e testar suas regras de Política para garantir a conformidade futura. A Política do Azure é direcionada ao nível da assinatura e fornece uma interface centralizada para gerenciar iniciativas, definições de política, resultados de auditoria e imposição e gerenciamento de exceções. Além das muitas definições internas do Azure Policy, os administradores podem criar suas próprias definições personalizadas por meio da modelagem de JSON simples. Para muitos clientes, a Microsoft recomenda a priorização da auditoria em detrimento da imposição, sempre que possível.

As seguintes políticas de exemplo podem ser úteis para cenários de conformidade do TIC:

|Política  |Cenário de exemplo  |Modelo inicial  |
|---------|---------|---------|
|Impor tabela de Rotas Definidas pelo Usuário |     Garantir que a rota padrão em todos os pontos de Redes Virtuais seja direcionada a um Gateway de Rede Virtual aprovado para o roteamento para o local | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Auditar se o Observador de Rede não está habilitado para a Região  | Garantir que o Observador de Rede esteja habilitado para todas as regiões usadas  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG X em cada sub-rede  | Garantir que um NSG (ou um conjunto de NSGs aprovados) com o tráfego da Internet bloqueado seja aplicado a todas as sub-redes em cada VNET | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X em cada NIC | Garantir que um NSG com o tráfego da Internet bloqueado seja aplicado a todas as NICs em todas as VMs. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Usar uma VNET aprovada para adaptadores de rede da VM  | Garantir que todas as NICs estejam em uma VNET aprovada | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Locais permitidos | Garantir que todos os recursos sejam implantados em regiões com uma configuração de VNETs e do Observador de Rede em conformidade  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Tipos de recurso não permitidos, como PublicIPs  | Proibir a implantação de tipos de recurso que não têm um plano de conformidade. Por exemplo, essa política pode ser usada para proibir a implantação de recursos de endereço IP Público. Embora as regras do NSG possam ser usadas para bloquear o tráfego da Internet com eficiência, evitar o uso de IPs Públicos reduz ainda mais a superfície de ataque.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>[Análise de Tráfego](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) do Azure

A Análise de Tráfego do Observador de Rede do Azure consome os dados de Log de fluxo e outros logs para fornecer uma visão geral de alto nível do tráfego de rede. Esses dados podem ser úteis para auditoria da conformidade do TIC e identificação de pontos problemáticos. Um painel de alto nível pode ser usado para fazer rapidamente uma triagem de quais VMs se comunicam com a Internet, que fornecerá uma lista voltada para o roteamento TIC.

![Captura de tela da Análise de Tráfego](media/tic-traffic-analytics-1.png)

Um "Mapa Geográfico" pode ser usado para identificar rapidamente os prováveis destinos físicos do tráfego da Internet para as VMs, permitindo que você identifique e faça uma triagem das alterações de padrão ou dos locais suspeitos.

![Captura de tela da Análise de Tráfego](media/tic-traffic-analytics-2.png)

Um mapa de topologia de rede pode ser usado para pesquisar rapidamente as redes virtuais existentes:

![Captura de tela da Análise de Tráfego](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Próximo salto do Observador de Rede do Azure

As redes nas regiões monitoradas pelo Observador de Rede podem conduzir testes de “Próximo Salto”, permitindo o acesso fácil e baseado em Portal ao tipo em uma origem e um destino para um fluxo de rede de exemplo, para o qual o Observador de Rede resolverá o destino de “Próximo Salto”. Isso pode ser usado em VMs e endereços na Internet de exemplo para garantir que o “Próximo Salto” seja, de fato, o Gateway de Rede Virtual.

![Próximo salto do Observador de rede](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusões

O acesso ao Microsoft Azure, ao Office 365 e ao Dynamics 365 pode ser configurado com facilidade para ajudar no cumprimento das diretrizes do TIC 2.0 – Apêndice H, conforme elaborado e definido em maio de 2018.  A Microsoft está ciente de que essas diretrizes estão sujeitas a alterações e fará o possível para ajudar os clientes a atender às diretrizes de maneira oportuna, conforme novas diretrizes forem liberadas.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Apêndice: Padrões do TIC para cargas de trabalho comuns

| Categoria | Carga de trabalho | IaaS | Injeção de PaaS/VNET dedicado  | Pontos de extremidade de serviço  |
|---------|---------|---------|---------|--------|
| Computação | Máquinas Virtuais do Linux | sim | | |
| Computação | Máquinas Virtuais do Windows | sim | | |
| Computação | Conjuntos de Dimensionamento de Máquinas Virtuais | sim | | |
| Computação | Funções do Azure | | por meio do ASE (Ambiente do Serviço de Aplicativo) | |
| Web e serviços móveis | Aplicativo Web interno | | por meio do ASE (Ambiente do Serviço de Aplicativo) | |
| Web e serviços móveis | Aplicativo móvel interno | | por meio do ASE (Ambiente do Serviço de Aplicativo) | |
| Web e serviços móveis | Aplicativos de API | | por meio do ASE (Ambiente do Serviço de Aplicativo) | |
| Contêineres | ACS (Serviço de Contêiner do Azure) | | | sim |
| Contêineres | AKS (Serviço de Contêiner do Azure)* | | | sim |
| Banco de dados | Banco de dados SQL | | Instância Gerenciada do Banco de Dados SQL do Azure* | SQL do Azure |
| Banco de dados | Banco de Dados do Azure para MySQL | | | sim |
| Banco de dados | Banco de Dados do Azure para PostgreSQL | | | sim |
| Banco de dados | SQL Data Warehouse | | | sim |
| Banco de dados | Azure Cosmos DB | | | sim |
| Banco de dados | Cache Redis | | sim | |
| Armazenamento | Blobs | sim | | |
| Armazenamento | Arquivos | sim | | |
| Armazenamento | Filas | sim | | |
| Armazenamento | Tabelas | sim | | |
| Armazenamento | Discos | sim | | |

**: versão prévia pública no Azure Governamental a partir de maio de 2018  
**: versão prévia privada no Azure Governamental a partir de maio de 2018

