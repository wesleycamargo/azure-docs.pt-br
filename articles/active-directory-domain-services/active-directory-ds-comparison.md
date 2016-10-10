<properties
	pageTitle="Azure AD Domain Services: comparar os Azure AD Domain Services com os controladores de domínio DIY | Microsoft Azure"
	description="Comparação dos Azure Active Directory Domain Services com controladores de domínio DIY"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="maheshu"/>

# Como decidir se os Azure AD Domain Services são adequados ao seu caso de uso
Os Azure AD Domain Services permitem que você implante suas cargas de trabalho nos Serviços de Infraestrutura do Azure, sem precisar se preocupar em manter sua infraestrutura de identidade. Esse serviço gerenciado é diferente de uma implantação típica do Windows Server Active Directory que você implanta e administra sozinho. O serviço foi desenvolvido para proporcionar implantação fácil, monitoramento e correção de integridade automatizados e uma infraestrutura de identidade simples para a nuvem. Estamos constantemente evoluindo o serviço a fim de adicionar suporte para cenários comuns de implantação.

Para decidir se você quer usar os Azure AD Domain Services ou criar e gerenciar sua própria infraestrutura de AD (faça você mesmo) no Azure:

- Veja a lista de [funcionalidades oferecidas pelos Azure AD Domain Services](active-directory-ds-features.md).

- Examine os [Cenários de implantação comuns dos Azure AD Domain Services](active-directory-ds-scenarios.md).

- Por fim, [compare os Azure AD Domain Services a uma opção de AD do tipo faça você mesmo](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## Compare os Azure AD Domain Services ao domínio do AD DIY no Azure
A tabela a seguir ajuda você a decidir entre usar os Azure AD Domain Services e gerenciar sua própria infraestrutura do Azure AD.

|Recurso|Azure AD Domain Services|AD "faça você mesmo" em VMs do Azure|
|---|:---:|:---:|
|[**Serviço gerenciado**](active-directory-ds-comparison.md#managed-service)|Sim|Não|
|[**Implantações seguras**](active-directory-ds-comparison.md#secure-deployments)|Sim|O administrador precisa proteger a implantação.|
|[**Servidor DNS**](active-directory-ds-comparison.md#dns-server)|Sim (serviço gerenciado)|Sim|
|[**Privilégios de administrador corporativo ou de domínio**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|Não|Sim|
|[**Ingresso no domínio**](active-directory-ds-comparison.md#domain-join)|Sim|Sim|
|[**Autenticação de domínio usando Kerberos e NTLM**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|Sim|Sim|
|[**Estrutura de UO personalizada**](active-directory-ds-comparison.md#custom-ou-structure)|Sim|Sim|
|[**Extensões de esquema**](active-directory-ds-comparison.md#schema-extensions)|Não|Sim|
|[**Relações de confiança de floresta/domínio do AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|Não|Sim|
|[**Leitura LDAP**](active-directory-ds-comparison.md#ldap-read)|Sim|Sim|
|[**LDAP Seguro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|Sim|Sim|
|[**Gravação LDAP**](active-directory-ds-comparison.md#ldap-write)|Não|Sim|
|[**Política de grupo**](active-directory-ds-comparison.md#group-policy)|Simples|Completo|
|[**Implantações geograficamente dispersas**](active-directory-ds-comparison.md#geo-dispersed-deployments)|Não|Sim|

#### Serviço gerenciado
Os domínios dos Azure AD Domain Services são gerenciados pela Microsoft. Você não precisa se preocupar com a aplicação de patches, atualizações, monitoramento, backups e em garantir a disponibilidade de seu domínio. Essas tarefas de gerenciamento são oferecidas como um serviço pelo Microsoft Azure para seus domínios gerenciados.

#### Implantações seguras
O domínio gerenciado é bloqueado com segurança, de acordo com as práticas de segurança recomendadas da Microsoft para implantações do AD. Essas práticas recomendadas derivam de décadas de experiência com engenharia e suporte a implantações do AD da equipe de produto do AD. Para implantações do tipo faça você mesmo, é necessário executar etapas de implantação específicas para bloquear/proteger sua implantação.

#### Servidor DNS
Um domínio gerenciado dos Azure AD Domain Services inclui serviços de DNS gerenciados. Os membros do grupo "Administradores de controlador de domínio AAD" podem gerenciar o DNS no domínio gerenciado. Os membros desse grupo recebem privilégios totais de Administração do DNS no domínio gerenciado. O gerenciamento de DNS pode ser executado usando o "Console de administração do DNS" incluído no pacote RSAT (Ferramentas de administração de servidor remoto).

#### Privilégios de administrador corporativo ou de domínio
Esses privilégios elevados não são oferecidos em um domínio gerenciado de DS AAD. Os aplicativos que exigem esses privilégios elevados para instalação/execução não podem ser executados em domínios gerenciados. Um subconjunto menor de privilégios administrativos está disponível para os membros do grupo de administração delegada chamado "Administradores de DC AAD". Esses privilégios incluem privilégios de configuração do DNS, configuração de política de grupo, obtenção de privilégios de administrador em computadores que ingressaram em domínio etc.

#### Ingresso no domínio
Você pode ingressar máquinas virtuais no domínio gerenciado de uma maneira parecida com o ingresso de computadores a um domínio do AD.

#### Autenticação de domínio usando Kerberos e NTLM
Com os Azure AD Domain Services, você pode usar suas credenciais corporativas para autenticar com o domínio gerenciado. As credenciais são mantidas em sincronia com seu locatário do Azure AD. Para locatários sincronizados, o Azure AD Connect garante que as alterações feitas nas credenciais localmente sejam sincronizadas com o Azure AD. Com uma configuração de domínio DIY, talvez seja necessário configurar uma relação de confiança de domínio com uma floresta de conta local para os usuários autenticarem usando suas credenciais corporativas. Como alternativa, talvez seja necessário configurar a replicação do AD a fim de garantir que as senhas de usuário sejam sincronizadas com suas máquinas de virtuais do controlador de domínio do Azure.

#### Estrutura de UO personalizada
Os membros do grupo "Administradores de controlador de domínio do AAD" podem criar UOs personalizadas dentro do domínio gerenciado.

#### Extensões de esquema
Você não pode estender o esquema de base de um domínio gerenciado dos Azure AD Domain Services. Portanto, os aplicativos que dependem de extensões ao esquema do AD (por exemplo, novos atributos no objeto do usuário) não podem ser deslocados para domínios do AAD-DS.

#### Relações de confiança de floresta ou domínio do AD
Os domínios gerenciados não podem ser configurados para estabelecer relações de confiança com outros domínios (entrada/saída). Portanto, cenários como implantações de floresta de recursos, ou em casos nos quais você preferir não sincronizar senhas para o Azure AD, não podem usar os Azure AD Domain Services.

#### Leitura LDAP
O domínio gerenciado oferece suporte a cargas de trabalho de leitura de LDAP. Portanto, você pode implantar aplicativos que realizam operações de leitura LDAP no domínio gerenciado.

#### LDAP seguro
Você pode configurar os Azure AD Domain Services para fornecer acesso seguro do LDAP ao seu domínio gerenciado, incluindo pela internet.

#### Gravação LDAP
O domínio gerenciado é somente leitura para objetos de usuário. Portanto, os aplicativos que executam operações de gravação LDAP em atributos do objeto do usuário não funcionam em um domínio gerenciado. Além disso, as senhas de usuário não podem ser alteradas dentro do domínio gerenciado. Outro exemplo seria a modificação das associações de grupo ou atributos de grupo dentro do domínio gerenciado, o que não é permitido. No entanto, quaisquer alterações em atributos de usuário ou senhas feitas no Azure AD (por meio do Portal do Azure/PowerShell) ou no AD local são sincronizadas com o domínio gerenciado do AAD-DS.

#### Política de grupo
Não há suporte para construtores de política de grupo sofisticados no domínio gerenciado do AAD-DS. Por exemplo, você não pode criar e implantar GPOs separados para cada UO personalizada no domínio, ou usar o filtro WMI para direcionamento de política de grupo. Há um GPO interno para os contêineres "Computadores AADDC" e "Usuários AADDC", que podem ser personalizados para configurar a política de grupo.

#### Implantações geograficamente dispersas
Os domínios gerenciados dos Azure AD Domain Services estão disponíveis em uma única rede virtual no Azure. Para os cenários que exigem a disponibilidade de controladores de domínio em várias regiões do Azure no mundo todo, configurar controladores de domínio em VMs IaaS do Azure pode ser a melhor alternativa.


## Opções de implantação de AD do tipo DIY (faça você mesmo)
Você pode ter casos de uso de implantação nos quais precisa de alguns dos recursos oferecidos por uma instalação de AD do Windows Server. Nesses casos, considere uma das seguintes opções de DIY (faça você mesmo):

- **Domínio de nuvem autônomo:** você pode configurar um "domínio de nuvem" autônomo usando máquinas virtuais do Azure que foram configuradas como controladores de domínio. Essa infraestrutura não se integra ao seu ambiente local do AD. Essa opção exigiria um conjunto diferente de "credenciais de nuvem" para fazer logon/administrar VMs na nuvem.

- **Implantação de floresta de recursos:** você pode configurar um domínio na topologia de floresta de recursos, usando máquinas virtuais do Azure configuradas como controladores de domínio. Em seguida, você pode configurar uma relação de confiança do AD com seu ambiente local do AD. Você pode ingressar computadores em domínio (VMs do Azure) nessa floresta de recursos na nuvem. A autenticação do usuário acontece em um uma conexão de VPN/ExpressRoute para seu diretório local.

- **Estender seu domínio local para o Azure:** você pode conectar uma rede virtual do Azure à sua rede local usando uma conexão VPN/ExpressRoute, para que as VMs do Azure possam ser ingressadas em seu AD local. Outra alternativa é promover uma réplica dos controladores de domínio de seu domínio local no Azure como uma VM. Você pode configurá-lo para replicar em uma conexão de VPN/ExpressRoute para seu diretório local. Esse modo de implantação estende efetivamente seu domínio local para o Azure.

> [AZURE.NOTE] Você pode determinar se uma opção de DIY é mais adequada para seus casos de uso de implantação. Considere [compartilhar comentários](active-directory-ds-contact-us.md) para nos ajudar a entender quais recursos ajudariam você a escolher os Azure AD Domain Services no futuro. Esses comentários nos ajudam a desenvolver o serviço a fim de atender melhor às suas necessidades de implantação e casos de uso.

Publicamos [Diretrizes para implantação do Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) para facilitar as instalações DIY.


## Conteúdo relacionado
- [Recursos – Azure AD Domain Services](active-directory-ds-features.md)

- [Cenários de implantação – Azure AD Domain Services](active-directory-ds-scenarios.md)

- [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

<!---HONumber=AcomDC_0928_2016-->