---
title: "Considerações de planejamento para a pilha do Azure sistemas integrados | Microsoft Docs"
description: "Saiba o que você pode fazer para planejar agora e preparar para pilha com vários nós do Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: cd14f0e5259e5c0b6cbf11790bbdf08164267ffa
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considerações de planejamento para a pilha do Azure sistemas integrados
Se você estiver interessado em um sistema de pilha do Azure integrado, você deve compreender alguns das principais considerações de planejamento em torno de implantação e como o sistema se encaixa no seu datacenter. Este artigo fornece uma visão geral dessas considerações para ajudá-lo a tomar decisões importantes de infra-estrutura para seu sistema de vários nó de pilha do Azure. Ajuda a entender essas considerações ao trabalhar com o fornecedor do hardware OEM implantação de pilha do Azure para seu datacenter.  

> [!NOTE]
> Sistemas de vários nós de pilha do Azure só podem ser adquiridos de fornecedores de hardware autorizado. 

Para implantar a pilha do Azure, há um conjunto das decisões que você precisa fazer para integrar corretamente a pilha do Azure com seu ambiente. Você precisa fornecer essas informações para seu provedor de soluções durante o processo de planejamento e estará pronto para o fornecedor de hardware antes do início da implantação ajudar o processo de forma rápida e sem problemas.

As informações necessárias intervalos em rede, segurança e informações de identidade com muitas decisões importantes que podem exigir conhecimento em muitas áreas diferentes e tomadores de decisão. Portanto, você talvez precise efetuar pull de pessoas de várias equipes em sua organização para garantir que você tenha todas as informações necessárias prontas antes do início da implantação. Ele pode ajudar a se comunicar com o fornecedor do hardware ao coletar essas informações, como talvez tenham conselhos úteis para tomar as decisões.

Durante a pesquisa e coletar as informações necessárias, você precisará fazer algumas alterações de configuração antes da implantação para seu ambiente de rede. Isso pode incluir a reserva de espaços de endereço IP para a solução de pilha do Azure e configurar seus roteadores, comutadores e firewalls para se preparar para a conectividade com as novas opções de solução de pilha do Azure. Certifique-se que o especialista de área de assunto alinhados e envolvidos no projeto de implantação para ajudá-lo com seu planejamento.

## <a name="management-considerations"></a>Considerações sobre gerenciamento
A pilha do Azure é um sistema de lacrado, onde a infraestrutura é bloqueada ambos a partir de um permissões e a perspectiva de rede. Listas de controle de acesso de rede (ACLs) são aplicadas para bloquear todo o tráfego de entrada não autorizado e todas as comunicações desnecessárias entre componentes de infraestrutura. Isso torna difícil para usuários não autorizados acessar o sistema.

Para operações e gerenciamento diário, há sem acesso de administrador sem restrições para a infraestrutura. Operadores de pilha do Azure devem gerenciar o sistema por meio do portal de administrador ou por meio do Azure Resource Manager (via CLI do Azure, o PowerShell ou a API REST). Não há nenhum acesso ao sistema por outras ferramentas de gerenciamento, como o Gerenciador do Hyper-V ou o Gerenciador de Cluster de Failover. Para ajudar a proteger o sistema, o software de terceiros (por exemplo, agentes) não pode ser instalado dentro dos componentes da infraestrutura de pilha do Azure. Interoperabilidade com o software de segurança e gerenciamento externo ocorre por meio de CLI do Azure, o PowerShell ou a API REST.

Quando um nível mais alto de acesso é necessário para a solução de problemas não resolvidos por meio de etapas de mediação de alerta, você deve trabalhar com suporte. Por meio do suporte, há um método para fornecer acesso de administrador completo temporário para o sistema para executar operações mais avançadas. 

## <a name="identity-considerations"></a>Considerações de identidade

### <a name="choose-identity-provider"></a>Escolha o provedor de identidade
Você precisará considerar qual provedor de identidade que você deseja usar para implantação de pilha do Azure, Azure AD ou AD FS. Não é possível alternar os provedores de identidade após a implantação sem reimplantação completa do sistema.

A opção de provedor de identidade não tem suporte em máquinas virtuais de locatário, o sistema de identidade e contas usarem, se eles podem ingressar em um domínio do Active Directory, etc. Isso é separado.

Você pode aprender mais sobre como escolher um provedor de identidade no [decisões de implantação para o artigo de sistemas integrados de pilha do Azure](.\azure-stack-deployment-decisions.md).

### <a name="ad-fs-and-graph-integration"></a>Integração do AD FS e gráfico
Se você optar por implantar a pilha do Azure usando o AD FS como o provedor de identidade, você deve integrar a instância do AD FS na pilha do Azure com uma instância existente do AD FS por meio de uma relação de confiança de Federação. Isso permite que as identidades em uma floresta existente do Active Directory para autenticar com recursos na pilha do Azure.

Você também pode integrar o serviço de gráfico na pilha do Azure com o Active Directory existente. Isso permite que você gerencie baseada em controle de acesso (RBAC) na pilha do Azure. Quando o acesso a um recurso é delegado, o componente gráfico procura a conta de usuário na floresta existente do Active Directory usando o protocolo LDAP.

O diagrama a seguir mostra o fluxo de tráfego de AD FS e o gráfico integrado.
![Diagrama mostrando o fluxo de tráfego do AD FS e gráfico](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licenciamento

Você deve decidir qual modelo de licenciamento que você deseja usar. Para uma implantação de conectado, você pode escolher o pagamento como você-uso ou baseado na capacidade de licenciamento. Pagamento como você-uso requer uma conexão para o Azure para relatar o uso, que, em seguida, é cobrado por meio de comércio do Azure. Licenciamento baseado apenas em capacidade terá suporte se você implantar desconectado da internet. Para obter mais informações sobre os modelos de licenciamento, consulte [pacotes e preços de pilha do Microsoft Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisões de nomenclatura

Você precisará pensar em como você deseja planejar seu namespace de pilha do Azure, especialmente o nome da região e o nome de domínio externo. O nome de domínio totalmente qualificado (FQDN) da sua implantação de pilha do Azure para pontos de extremidade públicos é a combinação desses dois nomes, &lt; *região*&gt;&lt;*external_FQDN*  &gt;, por exemplo, *east.cloud.fabrikam.com*. Neste exemplo, os portais de pilha do Azure está disponíveis nas seguintes URLs:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

A tabela a seguir resume essas decisões de nomeação de domínio.

| NOME | DESCRIÇÃO | 
| -------- | ------------- | 
|Nome de região | O nome da sua região do Azure pilha primeiro. Esse nome é usado como parte do FQDN para os endereços IP virtuais públicos (VIPs) que gerencia a pilha do Azure. Normalmente, o nome da região seria um identificador de local físico, como um local de datacenter. | 
| Nome de domínio externo | O nome da zona de sistema de nome de domínio (DNS) para pontos de extremidade com externo VIPs. Usado no FQDN para esses VIPs públicos. | 
| Nome de domínio (interno) privada | O nome do domínio (e zona DNS interna) criado na pilha do Azure para gerenciamento de infraestrutura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificado

Para a implantação, você precisará fornecer certificados Secure Sockets Layer (SSL) para pontos de extremidade públicos. Em um nível alto, os certificados têm os seguintes requisitos:

- Você pode usar um certificado curinga ou você pode usar um conjunto de certificados dedicados e usar caracteres curinga somente para pontos de extremidade como armazenamento e chave de cofre.
- Certificados podem ser emitidos por uma CA (autoridade de certificado confiável público) ou uma autoridade de certificação gerenciada pelo cliente.

Para obter mais informações sobre quais PKI certificados necessários para implantar a pilha do Azure e como obtê-las, consulte [requisitos de certificado da infraestrutura de chave pública do Azure pilha](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> As informações de certificado PKI fornecidas devem ser usadas como orientação geral. Antes de adquirir quaisquer certificados PKI para a pilha do Azure, trabalhe com seu parceiro de hardware de OEM. Eles fornecerão orientação de certificado e requisitos mais detalhados.



## <a name="time-synchronization"></a>Sincronização de horário
Você deve escolher um horário específico de servidor com é usado para sincronizar a pilha do Azure.  Sincronização de hora é essencial para a pilha do Azure e suas funções de infraestrutura, como ele é usado para gerar os tíquetes Kerberos que são usados para autenticar os serviços internos uns com os outros.

Você deve especificar um IP para o servidor de sincronização de hora, como Embora a maioria dos componentes da infra-estrutura pode resolver uma URL, algumas suporta apenas endereços IP. Se você estiver usando a opção de implantação desconectado, você deve especificar um servidor de horário em sua rede corporativa que você está se pode ser alcançado na rede de infraestrutura na pilha do Azure. Isso pode exigir uma consideração adicional durante o planejamento da parte de integração da rede do projeto de implantação.


## <a name="network-connectivity"></a>Conectividade de rede
Esta seção fornece informações de infraestrutura de rede da pilha do Azure que ajudarão você a tornar importantes decisões sobre a melhor integram pilha do Azure em seu ambiente de rede existente. 

> [!NOTE]
> Para resolver nomes DNS externos da pilha do Azure (por exemplo, www.bing.com), você precisará fornecer os servidores DNS que pilha do Azure pode usar para encaminhar solicitações DNS para o qual a pilha do Azure não está autorizada. Para obter mais informações sobre o Azure pilha DNS requisitos, consulte [integração de datacenter do Azure pilha - DNS](azure-stack-integrate-dns.md).

### <a name="physical-network-design"></a>Design de rede física
A solução de pilha do Azure exige uma infraestrutura física flexível e altamente disponível para dar suporte a seus serviços e a operação. Abaixo está um diagrama de nosso design recomendado:

![Design de rede recomendado pilha do Azure](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>Redes lógicas
Redes lógicas representam uma abstração da infraestrutura de rede física subjacente. Eles são usados para organizar e simplificar atribuições de rede para hosts, máquinas virtuais e serviços. Como parte da criação de rede lógica, sites de rede são criados para definir redes locais virtuais (VLANs), sub-redes IP e pares de sub-rede/VLAN IP que estão associados com a rede lógica em cada local físico.
A infraestrutura de rede para a pilha do Azure usa as seguintes redes lógicas que são configuradas nos switches:

### <a name="network-infrastructure"></a>Infraestrutura de rede
A infraestrutura de rede para a pilha do Azure consiste em várias redes lógicas que são configurados nos switches. O diagrama a seguir mostra essas redes lógicas e como integrar o top-of-rack (TOR) controlador BMC (BMC) e comutadores (rede de cliente) da borda.

![Conexões de diagrama e o comutador de rede lógica](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>Rede do BMC
Esta rede dedicada para se conectar a todos os controladores BMC (também conhecido como processadores de serviço, por exemplo, iDRAC iLO, iBMC, etc.) para a rede de gerenciamento. Se estiver presente, o host de ciclo de vida de hardware (HLH) estarão localizado na rede e pode fornecer o software específico do OEM para manutenção de hardware e/ou o monitoramento. 

#### <a name="private-network"></a>Rede privada
Este /24 (254 host IPs) é privada à região de pilha do Azure (não expandir além dos dispositivos de switch de borda da região de pilha do Azure) e é dividido em duas sub-redes de rede:

- **Rede de armazenamento**. Um /25 (126 host IPs) usado para dar suporte ao uso de espaços de armazenamento diretos (S2D) e o tráfego de armazenamento de bloco de mensagens de servidor (SMB) e a migração ao vivo de máquina virtual de rede. 
- **Rede IP virtual interna**. Um/25 rede VIPs dedicados a somente interno para o balanceador de carga de software (SLB).

#### <a name="azure-stack-infrastructure-network"></a>Rede de infraestrutura de pilha do Azure
Isso/24 de rede dedicado para componentes internos de pilha do Azure para que eles possam se comunicar e trocar dados entre si. Essa sub-rede requer endereços IP roteáveis, mas é mantida privada para a solução usando listas de controle de acesso (ACLs).  Não se espera que ele deve ser roteada além as opções de borda, exceto um intervalo muito pequena equivalente em tamanho um /27 rede, que é utilizada por alguns desses serviços quando eles requerem acesso a recursos externos e/ou a internet. 

#### <a name="public-infrastructure-network"></a>Rede de infraestrutura pública
Isso/27 de rede é muito pequeno intervalo de sub-rede de infraestrutura do Azure pilha mencionado anteriormente, ele não requer que os endereços IP públicos, mas exige acesso à internet por meio de um NAT ou um Proxy transparente. Esta rede será alocada para sistema de Console de recuperação de emergência (ERCS). A VM ERCS requer acesso à internet durante o registro para o Azure e deve ser roteável à sua rede de gerenciamento para fins de solução de problemas.

#### <a name="public-vip-network"></a>Rede pública de VIP
Rede pública VIP é atribuída ao controlador de rede na pilha do Azure. Não é uma rede lógica no comutador. O SLB usa o pool de endereços e atribui/32 redes para cargas de trabalho de locatário. Na tabela de roteamento de comutador, desses IPs 32 são publicados como uma rota disponível por meio do BGP. Esta rede contém os endereços IP externo acessível ou públicos. A infraestrutura de pilha do Azure usa pelo menos 8 endereços da rede pública VIP enquanto o restante é usado pelas máquinas virtuais do locatário. O tamanho da rede nesta sub-rede pode variar de um mínimo de /26 (64 hosts) a um máximo de /22 (1022 hosts), é recomendável que você planejar um /24 rede.

#### <a name="switch-infrastructure-network"></a>Rede de infraestrutura de chave
Isso/26 de rede é a sub-rede que contém sub-redes de IP de ponto a ponto roteável /30 (IP host 2) e o loopbacks que são dedicados/32 sub-redes de banda no comutador gerenciamento e a ID do roteador BGP. Este intervalo de endereços IP deve ser roteável externamente da solução de pilha do Azure para seu datacenter e podem ser IPs privadas ou públicas. Por exemplo, em um cenário de provedor de serviços com locatário vários que IPS públicos podem ser necessários, enquanto em uma implantação corporativa rigidamente controlada, IPs privados poderia ser preferencial.

#### <a name="switch-management-network"></a>Rede de gerenciamento de switch
Este /29 (6 host IPs) dedicada para conectar as portas de gerenciamento dos comutadores de rede. Ele permite acesso de fora da banda para implantação, gerenciamento e solução de problemas. Ele é calculado da rede de infraestrutura de comutador mencionado acima.

### <a name="transparent-proxy"></a>PROXY TRANSPARENTE
A solução de pilha do Azure não oferece suporte a proxies da web normal. Se o datacenter requer que todo o tráfego para usar um proxy, você deve configurar um proxy transparente para processar todo o tráfego do rack para tratá-la de acordo com a política, separar o tráfego entre as zonas em sua rede. Um proxy transparente (também conhecido como uma interceptação, embutido ou proxy forçado) intercepta comunicação normal na camada de rede sem a necessidade de qualquer configuração especial do cliente. Clientes não precisam estar cientes da existência do proxy.

### <a name="publish-azure-stack-services"></a>Publicar os serviços do Azure pilha

Você precisará disponibilizar os serviços do Azure pilha para os usuários da pilha fora do Azure. A pilha do Azure define vários pontos de extremidade para suas funções de infraestrutura. Esses pontos de extremidade são atribuídos os VIPs do pool de endereços IP público. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo, que foi especificado no momento da implantação. Por exemplo, o portal do usuário é atribuído a entrada do host DNS do portal.  *&lt;região >.&lt; FQDN >*.

#### <a name="ports-and-urls"></a>URLs e portas

Para tornar os serviços do Azure pilha (como os portais do Azure Resource Manager, DNS, etc.) disponíveis para redes externas, você deve permitir tráfego de entrada para esses pontos de extremidade específicos de URLs, portas e protocolos.
 
Em uma implantação onde uplink um proxy transparente em um servidor de proxy tradicional, você deve permitir URLs e portas específicas para comunicação de saída. Isso inclui URLs e portas de identidade, distribuição de mercado, patch e atualização, registro e dados de uso.

Para obter mais informações, confira:
- [Protocolos e portas de entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [URLs e portas de saída](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Conecte-se a pilha do Azure para o Azure

Para cenários de nuvem híbrida, você precisará planejar como você deseja se conectar a pilha do Azure para o Azure. Há dois métodos para se conectar a redes virtuais na pilha do Azure para redes virtuais no Azure: 
- **Site a site**. Uma conexão de rede virtual privada (VPN) através de IPsec (IKE v1 e IKE v2). Esse tipo de conexão exige um dispositivo VPN ou roteamento e acesso remoto (RRAS). Para obter mais informações sobre gateways VPN no Azure, consulte [sobre o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A comunicação por esse túnel é criptografada e é segura. No entanto, a largura de banda é limitada pela taxa de transferência máxima do túnel (100-200 Mbps).
- **NAT de saída**. Por padrão, todas as máquinas virtuais no Azure pilha terá conectividade a redes externas por meio de NAT de saída. Cada rede virtual que é criada na pilha do Azure obtém um endereço IP público atribuído a ele. Se a máquina virtual está diretamente atribuída a um endereço IP público ou estiver atrás de um balanceador de carga com um endereço IP público, ele terá acesso de saída por meio de NAT de saída usando o VIP da rede virtual. Isso funciona somente para a comunicação é iniciada pela máquina virtual e destinada a redes externas (internet ou intranet). Ele não pode ser usado para se comunicar com a máquina virtual de fora.

#### <a name="hybrid-connectivity-options"></a>Opções de conectividade híbrida

Para conectividade híbrida, é importante considerar que tipo de implantação que você quer oferecer e onde ele será implantado. Você precisará considerar se é necessário isolar o tráfego de rede por locatário, e se você terá uma implantação de internet ou intranet.

- **Pilha de único locatário do Azure**. Uma implantação de pilha do Azure que procura, pelo menos de uma perspectiva de rede, como se fosse um locatário. Pode haver que muitas assinaturas de locatários, mas como qualquer serviço de intranet, todo o tráfego percorram as mesmas redes. Tráfego de rede de uma assinatura percorram a mesma conexão de rede como outra assinatura e não precisa ser isoladas por meio de um túnel criptografado.

- **Pilha de multilocatária do Azure**. Uma implantação de pilha do Azure em que o tráfego do cada assinatura locatário associado para redes externas a pilha do Azure deve ser isolado de tráfego de rede de outros locatários.
 
- **Implantação de intranet**. Uma implantação de pilha do Azure que se encontra em uma intranet corporativa, normalmente no espaço de endereço IP privado e atrás de firewalls de um ou mais. Os endereços IP públicos não são realmente públicos, pois eles não podem ser roteados diretamente pela internet pública.

- **Implantação de Internet**. Uma implantação de pilha do Azure que está conectada ao público na internet e usa roteável pela internet pública endereços IP do intervalo de VIP público. A implantação ainda pode ser colocados atrás de um firewall, mas o intervalo de VIP público é diretamente acessível da internet pública e do Azure.
 
A tabela a seguir resume os cenários de conectividade híbrida, com casos de uso, prós e contras.

| Cenário | Método de conectividade | Prós | Contras | BOM para |
| -- | -- | --| -- | --|
| Único locatário do Azure pilha, implantação de intranet | NAT de saída | Melhor largura de banda para transferências mais rápidas. Simples de implementar; Não há gateways necessários. | Tráfego não criptografado; Não há isolamento ou criptografia além TOR. | Implantações de empresa onde todos os locatários são igualmente confiáveis.<br><br>Empresas que têm um circuito de rota expressa do Azure para o Azure. |
| Pilha de Azure multilocatário, implantação de intranet | VPN site a site | Tráfego do locatário de rede virtual para o destino é seguro. | Largura de banda é limitada pelo túnel VPN site a site.<br><br>Requer um gateway da rede virtual e um dispositivo VPN na rede de destino. | Implantações de empresa onde algumas tráfego de locatário devem ser protegidas de outros locatários. |
| Único locatário do Azure pilha, implantação de internet | NAT de saída | Melhor largura de banda para transferências mais rápidas. | Tráfego não criptografado; Não há isolamento ou criptografia além TOR. | Hospedagem de cenários em que o locatário obtém sua própria implantação de pilha do Azure e um circuito dedicado para o ambiente de pilha do Azure. Por exemplo, alternando de rótulo multiprotocolo (MPLS) e rota expressa.
| Pilha de Azure multilocatário, implantação de internet | VPN site a site | Tráfego do locatário de rede virtual para o destino é seguro. | Largura de banda é limitada pelo túnel VPN site a site.<br><br>Requer um gateway da rede virtual e um dispositivo VPN na rede de destino. | Onde quer que o provedor ofereça uma nuvem de multilocatária de cenários de hospedagem, em que os locatários não confia entre si e o tráfego deve ser criptografada.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Usando o ExpressRoute

Você pode se conectar a pilha do Azure para o Azure por meio de [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para cenários de multilocatários e de intranet de único locatário. Você precisará de um circuito de rota expressa provisionado por meio de [um provedor de conectividade](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

O diagrama a seguir mostra a rota expressa para um cenário de único locatário (onde "Conexão cliente" é o circuito de rota expressa).

![Cenário de rota expressa do diagrama mostrando único locatário](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

O diagrama a seguir mostra a rota expressa para um cenário de vários locatários.

![Cenário de rota expressa do diagrama mostrando multilocatário](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitoramento externo
Para obter uma única exibição de todos os alertas de seus dispositivos e a implantação da pilha do Azure e para integrar a IT service management fluxos de trabalho existentes para tíquetes de alertas, você pode integrar o Azure pilha com soluções de monitoramento de datacenter externo.

Incluído com a solução de pilha do Azure, o host de ciclo de vida de hardware (HLH) é um computador fora da pilha do Azure que executa as ferramentas de gerenciamento fornecida pelo fornecedor OEM para hardware. Você pode usar essas ferramentas ou outras soluções que integram-se diretamente a soluções de monitoramento existentes em seu data center.

A tabela a seguir resume a lista de opções disponíveis no momento.

| Área | Solução de monitoramento |
| -- | -- |
| Software de pilha do Azure | - [Pacote de gerenciamento de pilha do Azure para o Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Plug-in de Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-Chamadas de API baseado em REST | 
| Servidores físicos (BMCs via IPMI) | -Pacote de gerenciamento de fornecedor operations Manager<br>-Solução de fornecido pelo fornecedor de hardware OEM<br>-Fornecedor de hardware Nagios plug-ins | Suporte de parceiro (incluída) de solução de monitoramento de OEM | 
| Dispositivos de rede (SNMP) | -Descoberta de dispositivos de rede operations Manager<br>-Solução de fornecido pelo fornecedor de hardware OEM<br>-Opção de Nagios plug-in |
| Monitoramento de integridade de assinatura de locatário | - [Pacote de gerenciamento do System Center para o Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Observe os seguintes requisitos:
- A solução que você usar deve ser sem agente. Você não pode instalar agentes de terceiros dentro de componentes da pilha do Azure. 
- Se você quiser usar o System Center Operations Manager, isso requer o Operations Manager 2012 R2 ou Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres

Planejamento para backup e recuperação de desastres envolve o planejamento para ambas as Azure pilha infraestrutura subjacente que hospeda máquinas virtuais e serviços de PaaS e de dados e aplicativos de locatário. Você deve planejar para essas separadamente.

### <a name="protect-infrastructure-components"></a>Proteger os componentes de infraestrutura

A pilha do Azure backup componentes da infraestrutura para um compartilhamento que você especificar.

- Você precisará de um compartilhamento de arquivos SMB externo em um servidor de arquivos do Windows existente ou um dispositivo de terceiros.
- Você deve usar esse mesmo compartilhamento para o backup de comutadores de rede e o host de ciclo de vida de hardware. O fornecedor do hardware OEM ajuda a fornecer orientação para backup e restauração desses componentes conforme eles são externos a pilha do Azure. Você é responsável pela execução de fluxos de trabalho de backup com base na recomendação do fornecedor OEM.

Se ocorrer perda de dados catastrófica, você pode usar o backup de infraestrutura para dados de nova propagação de implantação, como certificado de raiz de autoridade de certificação de entradas e identificadores de contas de serviço de implantação, recursos federados (em implantações desconectados), planos, ofertas, assinaturas, cotas, atribuições de função e política RBAC e segredos de Cofre de chaves.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteger aplicativos de locatário em máquinas virtuais

A pilha do Azure não fazer backup de dados e aplicativos de locatário. Você deve planejar para proteção de recuperação de desastre e backup para um destino externo a pilha do Azure. Proteção de locatário é uma atividade controlada por locatário. Para máquinas virtuais de IaaS, os locatários podem usar tecnologias no convidado para proteger as pastas de arquivos, dados de aplicativo e o estado do sistema. No entanto, como uma empresa ou provedores de serviços, você talvez queira oferecem uma solução de backup e recuperação no mesmo datacenter ou externamente em uma nuvem.

Para fazer backup de máquinas virtuais Linux ou IaaS do Windows, você deve usar produtos de backup com acesso ao sistema operacional convidado para proteger o arquivo, pasta, estado do sistema operacional e dados de aplicativo. Você pode usar o Backup do Azure, o System Center Data Center Protection Manager, ou produtos de terceiros com suporte.

Para replicar dados para um local secundário e orquestrar failover de aplicativo se ocorrer um desastre, você pode usar o Azure Site Recovery ou produtos de terceiros com suporte. (Na versão inicial dos sistemas integrados, recuperação de Site do Azure não oferecem suporte a failback. No entanto, você pode obter failback por meio de um processo manual.) Além disso, os aplicativos que oferecem suporte à replicação nativa (como o Microsoft SQL Server) podem replicar dados para outro local onde o aplicativo está sendo executado.

> [!IMPORTANT]
> A versão inicial dos sistemas integrados, podemos será compatível com tecnologias de proteção que funcionam no nível de convidado de uma máquina virtual IaaS. Você não pode instalar agentes em servidores de infraestrutura subjacente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre os casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [Azure pilha](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o mapa e a disponibilidade de replicação geográfica do Azure pilha sistemas integrados, consulte o white paper: [pilha do Azure: extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
