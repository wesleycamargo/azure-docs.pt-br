---
title: Sistemas integrados de considerações sobre a integração geral de datacenter do Azure Stack | Microsoft Docs
description: Saiba o que você pode fazer para planejar agora e preparação para a integração do data center com vários nós do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 5ececb2d3c52a1da8c1a537e6223f17a9b83921f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207527"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Considerações de integração do Datacenter para sistemas integrados do Azure Stack
Se você estiver interessado em um sistema integrado do Azure Stack, você deve compreender as principais considerações de planejamento em torno de implantação e como o sistema se encaixa no seu datacenter. Este artigo fornece uma visão geral dessas considerações para ajudá-lo a tomar decisões de infraestrutura importante para o seu sistema de vários nó do Azure Stack. Ajuda a compreender essas considerações ao trabalhar com o fornecedor do hardware OEM como implantar o Azure Stack para seu datacenter.  

> [!NOTE]
> Sistemas de vários nós de pilha do Azure só podem ser adquiridos de fornecedores de hardware autorizado. 

Para implantar o Azure Stack, você precisa fornecer informações de planejamento para seu provedor de soluções antes de implantação é iniciada ajudar o processo de forma rápida e tranquila. As informações necessárias intervalos em rede, segurança e informações de identidade com muitas decisões importantes que podem exigir conhecimento de muitas áreas diferentes e tomadores de decisão. Portanto, você pode ter que puxar as pessoas de várias equipes em sua organização para garantir que você tenha todas as informações necessárias prontas antes do início da implantação. Ele pode ajudar a se comunicar com o fornecedor do hardware ao coletar essas informações, como talvez tenham conselhos úteis para tomar suas decisões.

Ao mesmo tempo pesquisando e coletar as informações necessárias, talvez você precise fazer algumas alterações de configuração de pré-implantação em seu ambiente de rede. Isso pode incluir a reserva de espaços de endereço IP para a solução do Azure Stack, configurar seus roteadores, comutadores e firewalls para se preparar para a conectividade com as novas opções de solução do Azure Stack. Certifique-se de ter o especialista de área de assunto com linhas até ajudá-lo com seus planos.

## <a name="capacity-planning-considerations"></a>Considerações de planejamento de capacidade
Ao avaliar uma solução de pilha do Azure para aquisição, opções de configuração de hardware devem ser feitas que têm um impacto direto na capacidade geral de sua solução do Azure Stack. Eles incluem as opções clássicas de CPU, densidade de memória, configuração de armazenamento e escala de solução geral (por exemplo, o número de servidores). Ao contrário de uma solução de virtualização tradicionais, não é aplicável a aritmética simple desses componentes para determinar a capacidade utilizável. O primeiro motivo é que o Azure Stack é projetado para hospedar os componentes de infraestrutura ou gerenciamento de dentro da solução em si. O segundo motivo é que alguns de capacidade da solução está reservado para dar suporte à resiliência; a atualização de software da solução de forma que minimiza a interrupção das cargas de trabalho de locatário. 

O [planilha do Planejador de capacidade do Azure Stack](https://aka.ms/azstackcapacityplanner) ajuda você a aproveitar informado decisões em relação ao planejamento de capacidade de duas maneiras: ambos os selecionando uma oferta de hardware e a tentativa de acordo com uma combinação de recursos ou definindo a carga de trabalho de pilha do Azure destina-se a executar para exibir o hardware disponível SKUs que dão suporte a ele. Por fim, a planilha destina-se como um guia para ajudar na tomada de decisões relacionadas ao Azure Stack planejamento e configuração. 

A planilha não se destina a servir como um substituto para a sua investigação e análise.  Microsoft faz declarações ou garantias, expressas ou implícitas, quanto às informações fornecidas dentro da planilha.



## <a name="management-considerations"></a>Considerações de gerenciamento
O Azure Stack é um sistema de sealed, onde a infraestrutura bloqueada ambos a partir de uma permissão e a perspectiva de rede. Listas de controle de acesso (ACLs) de rede são aplicadas para bloquear todo o tráfego de entrada não autorizado e todas as comunicações desnecessárias entre componentes de infraestrutura. Isso torna difícil para os usuários não autorizados acessar o sistema.

Para operações e gerenciamento diário, não há nenhum acesso de administrador sem restrições à infraestrutura. Operadores do Azure Stack devem gerenciar o sistema por meio do portal de administrador ou por meio do Azure Resource Manager (por meio do PowerShell ou a API REST). Não há nenhum acesso ao sistema por outras ferramentas de gerenciamento, como o Gerenciador Hyper-V ou o Gerenciador de Cluster de Failover. Para ajudar a proteger o sistema, o software de terceiros (por exemplo, agentes) não pode ser instalado dentro dos componentes da infraestrutura do Azure Stack. Interoperabilidade com o software de gerenciamento e segurança externo ocorre por meio do PowerShell ou a API REST.

Quando um nível mais alto de acesso é necessária para solução de problemas que não são resolvidos por meio de etapas de mediação de alerta, você deve trabalhar com o Microsoft Support. Por meio do suporte, há um método para fornecer acesso de administrador completo temporário para o sistema para executar operações mais avançadas. 

## <a name="identity-considerations"></a>Considerações sobre identidade

### <a name="choose-identity-provider"></a>Escolha o provedor de identidade
Você precisará considerar qual provedor de identidade que você deseja usar para implantação do Azure Stack, Azure AD ou AD FS. É possível alternar os provedores de identidade após a implantação sem reimplantação completa do sistema. Se você não possui a conta do AD do Azure e estiver usando uma conta fornecida pelo seu provedor de serviços de nuvem, e se você decidir mudar o provedor e usar um Azure AD diferente da conta, neste momento você terá que entre em contato com seu provedor de soluções para reimplantar a solução f ou você em seu custo.

Sua escolha de provedor de identidade não tem nenhuma relevância em máquinas virtuais de locatário, o sistema de identidade e as contas que eles usam, se eles podem ingressar em um domínio do Active Directory, etc. Isso é separado.

Você pode aprender mais sobre como escolher um provedor de identidade na [artigo de modelos de conexão de sistemas integrados do Azure Stack](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integração do AD FS e do gráfico
Se você optar por implantar o Azure Stack usando o AD FS como o provedor de identidade, você deve integrar a instância do AD FS no Azure Stack com uma instância existente do AD FS por meio de uma relação de confiança de Federação. Isso permite que as identidades em uma floresta existente do Active Directory para autenticar com recursos no Azure Stack.

Você também pode integrar o serviço de grafo no Azure Stack com o Active Directory existente. Isso permite que você gerencie baseado em função controle de acesso (RBAC) no Azure Stack. Quando o acesso a um recurso é delegado, o componente gráfico procura a conta de usuário na floresta do Active Directory existente usando o protocolo LDAP.

O diagrama a seguir mostra o fluxo de tráfego do AD FS e o Graph integrado.
![Diagrama mostrando o fluxo de tráfego do AD FS e o gráfico](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licenciamento
Você deve decidir qual modelo de licenciamento que você deseja usar. As opções disponíveis dependem se ou não implantar o Azure Stack conectado à internet:
- Para um [conectados implantação](azure-stack-connected-deployment.md), você pode escolher o licenciamento de pagamento-como-o uso ou baseada em capacidade. Pagamento-como-uso requer uma conexão para o Azure para uso do relatório, que, em seguida, é cobrado por meio de comércio do Azure. 
- Somente o licenciamento baseado em capacidade terá suporte se você [implantar desconectado](azure-stack-disconnected-deployment.md) da internet. 

Para obter mais informações sobre os modelos de licenciamento, consulte [pacotes e preços do Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Decisões de nomenclatura

Você precisará pensar sobre como você deseja planejar seu namespace do Azure Stack, especialmente o nome da região e o nome de domínio externo. O nome de domínio totalmente qualificado (FQDN) externo de sua implantação do Azure Stack para pontos de extremidade públicos é a combinação desses dois nomes: &lt; *região*&gt;.&lt; *fqdn*&gt;. Por exemplo, *east.cloud.fabrikam.com*. Neste exemplo, os portais do Azure Stack seria disponíveis nas seguintes URLs:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> O nome da região escolhida para sua implantação do Azure Stack deve ser exclusivo e aparecerá nos endereços do portal. 

A tabela a seguir resume essas decisões de nomeação de domínio.

| NOME | DESCRIÇÃO | 
| -------- | ------------- | 
|Nome da região | O nome da sua primeira região do Azure Stack. Esse nome é usado como parte do FQDN para os virtuais endereços IP públicos (VIPs) que gerencia o Azure Stack. Normalmente, o nome da região seria um identificador de local físico, como um local de datacenter.<br><br>O nome da região deve consistir apenas letras e números entre 0 a 9. Não há caracteres especiais, como "-" ou "#", etc. são permitidos.| 
| Nome de domínio externo | O nome da zona de sistema de nome de domínio (DNS) para pontos de extremidade com face externa VIPs. Usado no FQDN para esses VIPs públicos. | 
| Nome do domínio privado (interno) | O nome do domínio (e zona DNS interna) criada no Azure Stack para gerenciamento de infraestrutura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificado

Para a implantação, você precisará fornecer certificados de Secure Sockets Layer (SSL) para pontos de extremidade públicos. Em um alto nível, os certificados têm os seguintes requisitos:

- Você pode usar um certificado curinga ou você pode usar um conjunto de certificados dedicados e usar caracteres curinga somente para pontos de extremidade como o armazenamento e o Cofre de chaves.
- Certificados podem ser emitidos por uma CA (autoridade de certificado confiável público) ou uma autoridade de certificação gerenciadas pelo cliente.

Para obter mais informações sobre quais PKI certificados são necessários para implantar o Azure Stack e como obtê-las, consulte [requisitos de certificado de infra-estrutura de chave pública do Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> As informações de certificado PKI fornecidas devem ser usadas como orientação geral. Antes de adquirir os certificados PKI para o Azure Stack, trabalhe com seu parceiro de hardware de OEM. Eles fornecerá orientações sobre o certificado e requisitos mais detalhadas.


## <a name="time-synchronization"></a>Sincronização de horário
Você deve escolher um horário específico de servidor com é usado para sincronizar o Azure Stack.  Sincronização de hora é essencial para o Azure Stack e suas funções de infraestrutura, como ele é usado para gerar os tíquetes Kerberos que são usados para autenticar os serviços internos uns com os outros.

Você deve especificar que um IP para o servidor de sincronização de hora, embora a maioria dos componentes da infra-estrutura pode resolver uma URL, algumas só há suporte a endereços IP. Se você estiver usando a opção de implantação desconectado, você deve especificar um servidor de horário na sua rede corporativa que você está claro que pode ser atingido de rede de infraestrutura no Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Conectar-se a pilha do Azure para o Azure

Para cenários de nuvem híbrida, você precisará planejar como você deseja se conectar a pilha do Azure ao Azure. Há dois métodos com suporte para conectar redes virtuais no Azure Stack para redes virtuais no Azure: 
- **Site a site**. Uma conexão de rede virtual privada (VPN) sobre IPsec (IKE v1 e IKE v2). Esse tipo de conexão exige um dispositivo VPN ou o roteamento e acesso remoto (RRAS). Para obter mais informações sobre gateways de VPN no Azure, consulte [sobre o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A comunicação por esse túnel é criptografada e é segura. No entanto, a largura de banda é limitada pela taxa de transferência máxima do túnel (100 a 200 Mbps).
- **NAT de saída**. Por padrão, todas as máquinas virtuais no Azure Stack terão conectividade para redes externas por meio de NAT de saída. Cada rede virtual que é criado no Azure Stack obtém um endereço IP público atribuído a ele. Se a máquina virtual é atribuída diretamente um endereço IP público, ou estiver atrás de um balanceador de carga com um endereço IP público, ele terá acesso de saída por meio de NAT de saída usando o VIP da rede virtual. Isso funciona somente para a comunicação é iniciada pela máquina virtual e destinada a redes externas (internet ou intranet). Ele não pode ser usado para se comunicar com a máquina virtual de fora.

### <a name="hybrid-connectivity-options"></a>Opções de conectividade híbrida

Para conectividade híbrida, é importante considerar que tipo de implantação que você deseja oferecer e onde ele será implantado. Você precisará considerar se você precisa isolar o tráfego de rede por locatário e se você terá uma implantação de intranet ou internet.

- **Locatário único do Azure Stack**. Uma implantação do Azure Stack que se parece, pelo menos de uma perspectiva de rede, como se fosse um locatário. Pode haver que muitas assinaturas de locatários, mas como qualquer serviço de intranet, todo o tráfego viajará pelas mesmas redes. Tráfego de rede de uma assinatura percorre a mesma conexão de rede como outra assinatura e não precisa ser isolados por meio de um túnel criptografado.

- **Vários locatário do Azure Stack**. Uma implantação do Azure Stack em que o tráfego do cada assinatura locatário associado a redes externas para o Azure Stack deve ser isolado do tráfego de rede de outros locatários.
 
- **Implantação de intranet**. Uma implantação do Azure Stack que reside em uma intranet corporativa, normalmente no espaço de endereço IP privado e atrás de firewalls de um ou mais. Os endereços IP públicos não são verdadeiramente públicos, pois eles não podem ser roteados diretamente pela internet pública.

- **Implantação de Internet**. Endereços de internet e usa IP pública roteáveis na internet de uma implantação do Azure Stack que está conectada ao público para o intervalo de VIP público. A implantação ainda pode estar por trás de um firewall, mas o intervalo de VIP público é diretamente acessível da internet pública e do Azure.
 
A tabela a seguir resume os cenários de conectividade híbrida, com os prós, os contras e os casos de uso.

| Cenário | Método de conectividade | Prós | Contras | BOM para |
| -- | -- | --| -- | --|
| Único locatário do Azure Stack, implantação de intranet | NAT de saída | Largura de banda melhor transferências mais rápidas. Simples de implementar; Não há gateways necessários. | Tráfego não criptografado; Sem isolamento ou criptografia fora da pilha. | Implantação em empresas em que todos os locatários são igualmente confiáveis.<br><br>Empresas que têm um circuito do ExpressRoute para o Azure. |
| Vários locatários do Azure Stack, implantação de intranet | VPN site a site | O tráfego do locatário de rede virtual para o destino é seguro. | Largura de banda é limitada pelo túnel VPN site a site.<br><br>Requer um gateway na rede virtual e um dispositivo VPN da rede de destino. | Implantações empresariais onde algumas tráfego de locatário devem ser protegidas de outros locatários. |
| Único locatário do Azure Stack, implantação de internet | NAT de saída | Largura de banda melhor transferências mais rápidas. | Tráfego não criptografado; Sem isolamento ou criptografia fora da pilha. | Hospedar cenários em que o locatário obtém sua própria implantação do Azure Stack e um circuito dedicado para o ambiente do Azure Stack. Por exemplo, o ExpressRoute e comutação de rótulo multiprotocolo (MPLS).
| Vários locatários do Azure Stack, implantação de internet | VPN site a site | O tráfego do locatário de rede virtual para o destino é seguro. | Largura de banda é limitada pelo túnel VPN site a site.<br><br>Requer um gateway na rede virtual e um dispositivo VPN da rede de destino. | Cenários em que o provedor quer oferecer uma nuvem de multilocatário de hospedagem, em que os locatários não confiam entre si e o tráfego deve ser criptografado.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Usando o ExpressRoute

Azure Stack pode se conectar ao Azure por meio [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para intranet de locatário único e cenários de multilocatário. Você precisará de um circuito do ExpressRoute provisionado por meio [um provedor de conectividade](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

O diagrama a seguir mostra o ExpressRoute para um cenário de único locatário (onde "Conexão do cliente" é o circuito do ExpressRoute).

![Cenário de ExpressRoute do diagrama mostrando locatário único](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

O diagrama a seguir mostra o ExpressRoute para um cenário de multilocatário.

![Cenário de ExpressRoute do diagrama mostrando multilocatário](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitoramento externo
Para obter uma única exibição de todos os alertas de seus dispositivos e implantação do Azure Stack e integrar os alertas IT service management fluxos de trabalho existentes para a emissão de tíquetes, você pode [integrar o Azure Stack ao datacenter externodesoluçõesdemonitoramento](azure-stack-integrate-monitor.md).

Incluído com a solução do Azure Stack, o host de ciclo de vida do hardware é um computador fora do Azure Stack que executa as ferramentas de gerenciamento fornecido pelo fornecedor OEM para hardware. Você pode usar essas ferramentas ou outras soluções que se integram diretamente com as soluções de monitoramento existentes em seu datacenter.

A tabela a seguir resume a lista de opções disponíveis no momento.

| Área | Solução de monitoramento externa |
| -- | -- |
| Software de pilha do Azure | [Pacote de gerenciamento do Azure Stack para o Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Plug-in do Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Chamadas à API baseada em REST | 
| Servidores físicos (BMCs via IPMI) | Hardware de OEM - pacote de gerenciamento do fornecedor do Operations Manager<br>Solução de fornecido pelo fornecedor de hardware de OEM<br>Fornecedor do hardware plug-ins do Nagios | OEM suporte de parceiro (incluída) de solução de monitoramento | 
| Dispositivos de rede (SNMP) | Descoberta de dispositivo de rede do Operations Manager<br>Solução de fornecido pelo fornecedor de hardware de OEM<br>Comutador de Nagios plug-in |
| Monitoramento de integridade de assinatura de locatário | [Pacote de gerenciamento do System Center para o Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Observe os seguintes requisitos:
- A solução que você usar deve ser sem agente. Você não pode instalar agentes de terceiros dentro de componentes do Azure Stack. 
- Se você quiser usar o System Center Operations Manager, Operations Manager 2012 R2 ou Operations Manager 2016 é necessária.

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres

Planejamento de backup e recuperação de desastres envolve o planejamento para ambos os Azure Stack infraestrutura subjacente que hospeda máquinas virtuais IaaS e serviços de PaaS e de dados e aplicativos de locatário. Você deve planejar essas separadamente.

### <a name="protect-infrastructure-components"></a>Proteger os componentes de infraestrutura

Você pode [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md) componentes da infraestrutura para SMB compartilham que você especificar:

- Você precisará de um compartilhamento de arquivos SMB externo em um servidor de arquivos com base em Windows ou um dispositivo de terceiros.
- Você deve usar esse mesmo compartilhamento para o backup de comutadores de rede e o host de ciclo de vida do hardware. O fornecedor do hardware OEM ajudará a fornecer orientação para backup e restauração desses componentes, conforme eles são externos ao Azure Stack. Você é responsável por executar os fluxos de trabalho de backup com base na recomendação do fornecedor OEM.

Se ocorrer perda de dados catastrófica, você pode usar o backup de infraestrutura para dados de nova propagação de implantação, como implantação entradas e identificadores de contas de serviço de certificado da AC raiz, federados recursos (implantações desconectados), planos, ofertas, as assinaturas, cotas, as atribuições de função e política RBAC e segredos do Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteger aplicativos de locatário em máquinas virtuais de IaaS

O Azure Stack não faz backup de dados e aplicativos de locatário. Você deve planejar para a proteção de recuperação de desastre e backup para um destino externo para o Azure Stack. Locatário de proteção é uma atividade controlada por locatário. Para máquinas virtuais de IaaS, os locatários podem usar a tecnologias no convidado para proteger as pastas de arquivos, dados de aplicativo e do estado do sistema. No entanto, como um provedor de serviço ou enterprise, você talvez queira oferecem uma solução de backup e recuperação no mesmo datacenter ou externamente em uma nuvem.

Para fazer backup de máquinas virtuais Linux ou Windows IaaS, você deve usar produtos de backup com acesso ao sistema operacional convidado para proteger o arquivo, pasta, estado do sistema operacional e dados de aplicativo. Você pode usar o Backup do Azure, System Center Data Center Protection Manager, ou produtos de terceiros com suporte.

Para replicar dados para um local secundário e orquestrar o failover de aplicativo se ocorrer um desastre, você pode usar o Azure Site Recovery ou produtos de terceiros com suporte. Além disso, os aplicativos que dão suporte à replicação nativa, como o Microsoft SQL Server, podem replicar dados para outro local em que o aplicativo está em execução.

## <a name="learn-more"></a>Saiba mais

- Para obter informações sobre casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o roteiro e a disponibilidade geográfica para o Azure Stack, sistemas integrados, consulte o white paper: [Azure Stack: Uma extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Próximas etapas
[Modelos de conexão de implantação de pilha do Azure](azure-stack-connection-models.md)
