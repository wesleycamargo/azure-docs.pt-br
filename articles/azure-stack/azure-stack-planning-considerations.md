---
title: "Considerações de planejamento para a pilha do Azure sistemas integrados | Microsoft Docs"
description: "Saiba o que você pode fazer para planejar agora e preparar para pilha com vários nós do Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: twooley
ms.openlocfilehash: 8484f7947f23a00c05b34babf13cd75f9d227740
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considerações de planejamento para a pilha do Azure sistemas integrados

*Aplica-se a: sistemas integrados de pilha do Azure*

Se você estiver interessado em um sistema de pilha do Azure integrado, você desejará entender alguns das principais considerações de planejamento em torno de implantação e como o sistema se encaixa no seu datacenter. Este tópico fornece uma visão geral dessas considerações.

Se você decidir comprar um sistema integrado, o fornecedor do hardware fabricante (OEM) ajuda a guiá-lo na maior parte do processo de planejamento em mais detalhes. Eles também executará a implantação real.

## <a name="management-considerations"></a>Considerações sobre gerenciamento

A pilha do Azure é um sistema de lacrado, onde a infraestrutura é bloqueada ambos a partir de um permissões e a perspectiva de rede. Listas de controle de acesso de rede (ACLs) são aplicadas para bloquear todo o tráfego de entrada não autorizado e todas as comunicações desnecessárias entre componentes de infraestrutura. Isso torna difícil para usuários não autorizados acessar o sistema.

Para operações e gerenciamento diário, há sem acesso de administrador sem restrições para a infraestrutura. Operadores de pilha do Azure devem gerenciar o sistema por meio do portal de administrador ou por meio do Azure Resource Manager (por meio do PowerShell ou a API REST). Não há nenhum acesso ao sistema por outras ferramentas de gerenciamento, como o Gerenciador do Hyper-V ou o Gerenciador de Cluster de Failover. Para ajudar a proteger o sistema, o software de terceiros (por exemplo, agentes) não pode ser instalado dentro dos componentes da infraestrutura de pilha do Azure. Interoperabilidade com o software de gerenciamento e segurança externa ocorre por meio do PowerShell ou a API REST.

Quando um nível mais alto de acesso é necessário para a solução de problemas não resolvidos por meio de etapas de mediação de alerta, você deve trabalhar com suporte. Por meio do suporte, há um método para fornecer acesso de administrador completo temporário para o sistema para executar operações mais avançadas. 

## <a name="deploy-connected-or-disconnected"></a>Implantar conectado ou desconectado
 
Você pode optar por implantar o Azure pilha conectada à internet (e no Azure) ou desconectado. Para obter o máximo proveito da pilha do Azure, incluindo cenários híbridos entre a pilha do Azure e o Azure, você deseja implantar conectado ao Azure. A tabela a seguir resume as principais diferenças entre os modos de implantação.

| Área | Modo conectado | Modo desconectado |
| -------- | ------------- | ----------|
| Provedor de identidade | Azure Active Directory (AD do Azure) ou serviços de Federação do Active Directory (AD FS) | Somente AD FS |
| Distribuição do Marketplace | Fazer o download de itens diretamente no Azure Marketplace para o marketplace na pilha do Azure | Requer o gerenciamento manual do marketplace na pilha do Azure |
| Modelo de licenciamento | Com capacidade ou pagamento como você-uso | Baseado na capacidade apenas|
| Patch e atualização  | Baixe os pacotes de atualização diretamente para a pilha do Azure | Requer uma mídia removível e um dispositivo conectado separado |
| | | |

Você não pode alterar o modo de implantação mais tarde sem reimplantação completa do sistema.

## <a name="identity-considerations"></a>Considerações de identidade

### <a name="choose-identity-provider"></a>Escolha o provedor de identidade

Você precisará considerar qual provedor de identidade que você deseja usar para implantação de pilha do Azure, Azure AD ou AD FS. Não é possível alternar os provedores de identidade após a implantação sem reimplantação completa do sistema.

A opção de provedor de identidade não tem suporte em máquinas virtuais de locatário, o sistema de identidade e contas usarem, se eles podem ingressar em um domínio do Active Directory, etc. Isso é separado.

**Motivos para considerar o uso do AD do Azure**

- Você já tem os investimentos já feitos no AD do Azure (como o Azure ou Office 365).
- Você deseja usar a mesma identidade entre nuvens do Azure e a pilha do Azure.
- Você deseja dar suporte a cenários de multilocação, onde você pode hospedar diferentes organizações na mesma instância de pilha do Azure.
- Você deseja usar o gerenciamento de diretório baseado em REST por meio do Azure AD Graph para provisionar usuários, grupos, etc., por meio de APIs.

> [!NOTE]
> Você não pode conectar uma implantação de pilha do Azure para uma instância do AD do Azure e uma instância existente do AD FS ao mesmo tempo. Se você implantar com o Azure AD, e você gostaria de usar uma instância existente do AD FS, você pode federar o local a instância do AD FS com o Azure AD.

**Motivos para considerar o uso do AD FS**

- Não há nenhum ou apenas parcial conectividade com a internet.
- Existem requisitos normativos/Soberania.
- Você deseja usar seu próprio sistema de identidade (como o Active Directory corporativo) para o operador e contas de usuário. Para fazer isso, você pode federar com uma instância existente do AD FS (no Windows Server 2012, 2012 R2 ou o 2016) que é feita pelo Active Directory.
- Você tem sem investimentos do Azure, e você não quiser usar o AD do Azure.

> [!NOTE]
> Você pode federar a pilha do Azure somente com outra instância do AD FS que é feita pelo Active Directory. Não há suporte para outros provedores de identidade. Se você tiver outros provedores de identidade que você deseja usar com a pilha do Azure, considere o uso de implantação baseado no AD do Azure.

### <a name="ad-fs-and-graph-integration"></a>Integração do AD FS e gráfico

Se você optar por implantar a pilha do Azure usando o AD FS como o provedor de identidade, você deve integrar a instância do AD FS na pilha do Azure com uma instância existente do AD FS por meio de uma relação de confiança de Federação. Isso permite que as identidades em uma floresta existente do Active Directory para autenticar com recursos na pilha do Azure.

Você também pode integrar o serviço de gráfico na pilha do Azure com o Active Directory existente. Isso permite que você gerencie baseada em controle de acesso (RBAC) na pilha do Azure. Quando o acesso a um recurso é delegado, o componente gráfico procura a conta de usuário na floresta existente do Active Directory usando o protocolo LDAP.

O diagrama a seguir mostra o fluxo de tráfego de AD FS e o gráfico integrado.
![Diagrama mostrando o fluxo de tráfego do AD FS e gráfico](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licenciamento

Você deve decidir qual modelo de licenciamento que você deseja usar. Para uma implantação de conectado, você pode escolher o pagamento como você-uso ou baseado na capacidade de licenciamento. Pagamento como você-uso requer uma conexão para o Azure para relatar o uso, que, em seguida, é cobrado por meio de comércio do Azure. Licenciamento baseado apenas em capacidade terá suporte se você implantar desconectado da internet. Para obter mais informações sobre os modelos de licenciamento, consulte [pacotes e preços de pilha do Microsoft Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisões de nomenclatura

Você precisará pensar em como você deseja planejar seu namespace de pilha do Azure, especialmente o nome da região e o nome de domínio externo. O nome de domínio totalmente qualificado (FQDN) da sua implantação de pilha do Azure para pontos de extremidade públicos é a combinação desses dois nomes, &lt; *região*&gt;&lt;*external_FQDN*  &gt;, por exemplo, *east.cloud.fabrikam.com*. Neste exemplo, os portais de pilha do Azure está disponíveis nas seguintes URLs:

- https://portal.east.cloud.Fabrikam.com
- https://adminportal.east.cloud.Fabrikam.com

A tabela a seguir resume essas decisões de nomeação de domínio.

| Nome | Descrição | 
| -------- | ------------- | 
|Nome da região | O nome da sua região do Azure pilha primeiro. Esse nome é usado como parte do FQDN para os endereços IP virtuais públicos (VIPs) que gerencia a pilha do Azure. Normalmente, o nome da região seria um identificador de local físico, como um local de datacenter. | 
| Nome de domínio externo | O nome da zona de sistema de nome de domínio (DNS) para pontos de extremidade com externo VIPs. Usado no FQDN para esses VIPs públicos. | 
| Nome de domínio (interno) privada | O nome do domínio (e zona DNS interna) criado na pilha do Azure para gerenciamento de infraestrutura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificado

Para a implantação, você precisará fornecer certificados Secure Sockets Layer (SSL) para pontos de extremidade públicos. Em um nível alto, os certificados têm os seguintes requisitos:

> [!IMPORTANT]
> As informações de certificado neste artigo são fornecidas apenas como guia Geral. Antes de adquirir os certificados para a pilha do Azure, trabalhe com seu parceiro de hardware de OEM. Eles fornecerão orientação de certificado e requisitos mais detalhados.

- Você pode usar um certificado curinga ou você pode usar um conjunto de certificados dedicados e usar caracteres curinga somente para pontos de extremidade como armazenamento e chave de cofre.
- Certificados devem ser emitidos por uma autoridade de certificado confiável pública (CA) ou uma autoridade de certificação gerenciada pelo cliente.
 
A tabela a seguir mostra os serviços e o número de pontos de extremidade públicos que requerem certificados para implantação inicial de pilha do Azure. 

| Usadas para | Ponto de extremidade 
| -------- | ------------- | 
| Gerenciador de recursos do Azure (administrador) | adminmanagment. [Região]. [external_domain] |
| Gerenciador de recursos do Azure (usuário) | gerenciamento. [Região]. [external_domain] |
| Portal (administrador) | adminportal. [Região]. [external_domain] |
| Portal (usuário) | Portal. [Região]. [external_domain] |
| Cofre de chaves (usuário) | &#42;. cofre. [Região]. [external_domain] |
| Cofre de chaves (administrador) | &#42;. adminvault. [Região]. [external_domain] |
| Armazenamento | &#42;. blob. [Região]. [external_domain]<br>&#42;. tabela. [Região]. [external_domain]<br>&#42;. fila. [Região]. [external_domain]  |
| Gráfico de * * | gráfico. [Região]. [external_domain] |
| AD FS * * | ADFS. [Região]. [external_domain] |
| | |

* * Certificados para pontos de extremidade do AD FS e de gráfico são necessárias somente para implantações do AD FS.

Se você quiser usar um certificado curinga, é necessário um total de seis Subject Alternative Names (SANs) para a implantação inicial de pilha do Azure. Essas SANs são: 

- &#42;. [Região]. [external_domain]
- &#42;. cofre. [Região]. [external_domain]
- &#42;. adminvault. [Região]. [external_domain]
- &#42;. blob. [Região]. [external_domain]
- &#42;. tabela. [Região]. [external_domain] 
- &#42;. fila. [Região]. [external_domain]

## <a name="time-synchronization"></a>Sincronização de horário

Você deve sincronizar o servidor de horário de pilha do Azure com um servidor de horário externo que pode ser resolvido por meio do endereço IP. Um servidor de horário na rede corporativa é necessário para uma implantação desconectada.

## <a name="network-connectivity"></a>Conectividade de rede

### <a name="dns-integration"></a>Integração do DNS

Para resolver nomes DNS externos da pilha do Azure (por exemplo, www.bing.com), você precisará fornecer os servidores DNS que pilha do Azure pode usar para encaminhar solicitações DNS para o qual a pilha do Azure não está autorizada. Como entradas de implantação, você deve fornecer pelo menos dois servidores para usar como encaminhadores DNS para tolerância a falhas.

Para resolver nomes DNS de pontos de extremidade de pilha do Azure da pilha fora do Azure (por exemplo, da floresta corporativa), você deve integrar os servidores DNS que hospedam a zona DNS externa para a pilha do Azure com os servidores DNS que hospedam a zona pai que você deseja usar. Isso exige que a resolução de nome DNS entre a pilha do Azure e zonas DNS existentes no datacenter. Para fazer isso, você usará métodos, como a delegação de zona ou encaminhamento condicional. É recomendável se você tem controle direto sobre os servidores DNS que hospedam a zona pai para seu namespace DNS externo do Azure pilha de encaminhamento condicional. (Se a zona DNS de pilha do Azure externa aparece como um domínio filho de seu nome de domínio corporativo (por exemplo, "azurestack.contoso.com" e "contoso.com"), você deve usar a delegação de zona.

### <a name="network-infrastructure"></a>Infraestrutura de rede

A infraestrutura de rede para a pilha do Azure consiste em várias redes lógicas que são configurados nos switches. O diagrama a seguir mostra essas redes lógicas e como integrar o top-of-rack (TOR) controlador BMC (BMC) e comutadores (rede de cliente) da borda.

![Conexões de diagrama e o comutador de rede lógica](media/azure-stack-planning-considerations/NetworkDiagram.png)

A tabela a seguir mostra as redes lógicas e os intervalos de sub-rede IPv4 associados que você deve planejar.

| Rede lógica | Descrição | Tamanho | 
| -------- | ------------- | ------------ | 
| VIP público | Endereços IP públicos para um pequeno conjunto de serviços de pilha do Azure, com o restante usados por máquinas virtuais de locatário. A infraestrutura de pilha do Azure usa 32 endereços da rede. Se você planeja usar o serviço de aplicativo e os provedores de recursos do SQL, isso usa mais de 7. | / 26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestrutura de chave | Endereços IP de ponta a ponta para fins de roteamento, dedicados alternar interfaces de gerenciamento e os endereços de loopback atribuídos ao comutador. | / 26 | 
| Infraestrutura | Usado para componentes internos da pilha do Azure para se comunicar. | /24 |
| Privado | Usado para a rede de armazenamento e os VIPs privadas. | /24 | 
| BMC | Usado para se comunicar com BMCs em hosts físicos. | / 27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Uplink para opções de borda

Você precisará de um uplink configurado para as opções de borda em seu data center. Você pode rotear esse tráfego de camada 3 das chaves top-of-rack (TOR) que fazem parte de um sistema de pilha do Azure integradas usando qualquer um dos seguintes métodos:

- Border Gateway Protocol (BGP) 
- roteamento estático

É recomendável BGP porque ela permite que a atualização automática de rotas que são publicadas pelo Software de carga balanceamento multiplexador (SLB MUX) para redes externas. Isso é importante se você adicionar intervalos de endereços IP públicos após a implantação. Se você fizer o emparelhamento de BGP TOR alterna para o comutador de agregação que estão conectados os comutadores TOR à, intervalos de endereço IP público que você adicionar pós-implantação são anunciado automaticamente para a opção de agregação sem intervenção manual. Se você usar o roteamento estático, você deve atualizar manualmente as rotas para os novos intervalos de cada vez que adicionar um bloco de endereço IP público.

### <a name="proxy-server"></a>Servidor proxy

Pilha do Azure oferece suporte somente a servidores de proxy transparente. Um proxy transparente intercepta solicitações na camada de rede sem a necessidade de qualquer configuração especial do cliente.

### <a name="publish-azure-stack-services"></a>Publicar os serviços do Azure pilha

Você precisará disponibilizar os serviços do Azure pilha para os usuários da pilha fora do Azure. A pilha do Azure define vários pontos de extremidade para suas funções de infraestrutura. Esses pontos de extremidade são atribuídos os VIPs do pool de endereços IP público. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo, que foi especificado no momento da implantação. Por exemplo, o portal do usuário é atribuído a entrada do host DNS do "portal. <*região*>. <*external_FQDN*>." 

#### <a name="ports-and-urls"></a>URLs e portas

Para tornar os serviços do Azure pilha (como os portais do Azure Resource Manager, DNS, etc.) disponíveis para redes externas, você deve permitir tráfego de entrada para esses pontos de extremidade específicos de URLs, portas e protocolos.
 
Em uma implantação onde uplink um proxy transparente em um servidor de proxy tradicional, você deve permitir URLs e portas específicas para comunicação de saída. Isso inclui URLs e portas de identidade, distribuição de mercado, patch e atualização, registro e dados de uso.

Para obter mais informações, consulte:
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

#### <a name="using-expressroute"></a>Uso da rota expressa

Você pode se conectar a pilha do Azure para o Azure por meio de [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para cenários de multilocatários e de intranet de único locatário. Você precisará de um circuito de rota expressa provisionado por meio de [um provedor de conectividade](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

O diagrama a seguir mostra a rota expressa para um cenário de único locatário (onde "Conexão cliente" é o circuito de rota expressa).

![Cenário de rota expressa do diagrama mostrando único locatário](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

O diagrama a seguir mostra a rota expressa para um cenário de vários locatários.

![Cenário de rota expressa do diagrama mostrando multilocatário](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitoramento externo
Para obter uma única exibição de todos os alertas de seus dispositivos e a implantação da pilha do Azure e para integrar a IT service management fluxos de trabalho existentes para tíquetes de alertas, você pode integrar o Azure pilha com soluções de monitoramento de datacenter externo.

Incluído com a solução de pilha do Azure, o host de ciclo de vida de hardware é um computador fora da pilha do Azure que executa as ferramentas de gerenciamento fornecida pelo fornecedor OEM para hardware. Você pode usar essas ferramentas ou outras soluções que integram-se diretamente a soluções de monitoramento existentes em seu data center.

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
