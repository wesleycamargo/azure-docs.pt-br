<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: visão geral | Microsoft Azure"
	description="Visão geral dos Serviços de Domínio do AD do Azure"
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
	ms.date="12/16/2015"
	ms.author="maheshu"/>

# Serviços de domínio do AD do Azure *(visualização)*

## Visão geral
Os Serviços de Infraestrutura do Azure permitem que você implante uma ampla gama de soluções de computação de maneira ágil. Com máquinas virtuais do Azure, você pode implantar quase instantaneamente e só paga por cada minuto. Usando suporte para Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, você pode implantar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operacional. Esses benefícios permitem migrar aplicativos herdados implantados no local para o Azure a fim de economizar em despesas operacionais.

Um aspecto importante da migração de aplicativos locais para o Azure está no tratamento das necessidades de identidade desses aplicativos. Aplicativos com reconhecimento de diretório podem utilizar LDAP em acesso para leitura ou gravação no diretório corporativo ou confiar na autenticação integrada do Windows (autenticação Kerberos ou NTLM) para autenticar os usuários finais. Linhas de aplicativos de negócios em execução no Windows Server normalmente são implantadas em computadores ingressados no mesmo domínio, para que possam ser gerenciadas com segurança usando a Política de grupo. Para poder “fazer a mudança” dos aplicativos para a nuvem, essa dependência da infraestrutura de identidade corporativa precisa ser resolvida.

Para atender às necessidades de identidade de seus aplicativos implantados no Azure, os administradores geralmente vão buscar uma das seguintes soluções:

- Implantar uma conexão VPN site a site entre as cargas de trabalho em execução nos Serviços de Infraestrutura do Azure e o diretório corporativo local.
- Estender a infraestrutura corporativa floresta/domínio do AD configurando réplicas dos controladores de domínio com as máquinas virtuais do Azure.
- Implantar um domínio autônomo no Azure usando controladores de domínio implantados como máquinas virtuais do Azure.

Todas essas abordagens tem o defeito de ser de alto custo e gerar sobrecarga administrativa. Os administradores devem implantar controladores de domínio usando as máquinas virtuais no Azure. Eles precisam posteriormente gerenciar, proteger, aplicar patch, monitorar, fazer backup e solucionar problemas dessas máquinas virtuais. O apoio em conexões VPN com o diretório local faz com que as cargas de trabalho implantadas no Azure fiquem vulneráveis a problemas de rede ou interrupções transitórias. Por sua vez, isso resulta em menor tempo de atividade e reduz a confiabilidade desses aplicativos.

Os Serviços de Domínio do AD do Azure são projetados para permitir uma alternativa muito mais fácil.


## Apresentando os Serviços de Domínio do AD do Azure
Os Serviços de Domínio do AD do Azure fornecem serviços de domínio gerenciado, como ingresso no domínio, política de grupo, LDAP, autenticação Kerberos/NTLM e outros. que são totalmente compatíveis com o Active Directory do Windows Server. Os Serviços de Domínio do AD do Azure permitem consumir esses serviços de domínio sem a necessidade de implantar, gerenciar e aplicar patches em controladores de domínio na nuvem. Os Serviços de Domínio do AD do Azure se integram ao locatário existente do Azure AD, permitindo aos usuários fazer logon usando suas credenciais corporativas. Além disso, você pode usar contas de usuário e grupos existentes para proteger o acesso a recursos, garantindo uma “mudança” mais tranquila dos recursos locais para os Serviços de Infraestrutura do Azure.

Os Serviços de Domínio do AD do Azure funcionam perfeitamente, esteja o locatário do Azure AD somente em nuvem ou sincronizado com o Active Directory local.

### Serviços de domínio do AD do Azure para organizações somente em nuvem
Um locatário do Azure AD somente em nuvem (também conhecidos como 'locatários gerenciados') não tem qualquer superfície de identidade no local. Em outras palavras, os usuários, suas senhas e associações de grupo são todos nativos para a nuvem, ou seja, criados e gerenciados no Azure AD. Considere por um momento que a Contoso é um locatário somente em nuvem do Azure AD. Como mostra a ilustração abaixo, o administrador da Contoso configurou uma rede virtual nos Serviços de Infraestrutura do Azure. Aplicativos e cargas de trabalho de servidor são implantados nessa rede virtual em máquinas virtuais do Azure. Como a Contoso é um locatário somente em nuvem, todas as identidades de usuário, suas credenciais e associações de grupo são criadas e gerenciadas no Azure AD.

![Visão geral dos Serviços de Domínio do AD do Azure](./media/active-directory-domain-services-overview/aadds-overview.png)

O administrador de TI da Contoso pode habilitar os Serviços de Domínio do AD do Azure para seu locatário do Azure AD e optar por disponibilizar os serviços de domínio nessa rede virtual. Quando isso é configurado, os Serviços de Domínio do AD do Azure provisionam um domínio gerenciado e o disponibilizam na rede virtual. Todas as contas, associações de grupo e credenciais do usuário disponíveis no locatário Azure AD da Contoso também estão disponíveis nesse domínio recém-criado. Esse recurso permite aos usuários entrar no domínio usando suas credenciais corporativas, por exemplo, quando se conectam remotamente a computadores ingressados no domínio pela área de trabalho remota. Os administradores podem provisionar acesso a recursos no domínio usando associações a grupos existentes. Aplicativos implantados em máquinas virtuais na rede virtual se beneficiam de serviços de domínio como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos, política de grupo, etc.

Alguns aspectos de destaque do domínio gerenciado que é provisionado pelos Serviços de Domínio do AD do Azure são os seguintes:

- O administrador de TI da Contoso não precisa gerenciar, fazer patch ou monitorar esse domínio ou qualquer controlador de domínio para o domínio gerenciado.
- Não é necessário gerenciar a replicação do AD para este domínio. Contas de usuário, associações de grupo e credenciais de locatário do Azure AD da Contoso ficam automaticamente disponíveis nesse domínio gerenciado.
- Uma vez que o domínio é gerenciado pelos Serviços de Domínio do AD do Azure, o administrador de TI da Contoso não tem privilégios de administrador corporativo ou de administrador de domínio nesse domínio.


### Serviços de Domínio do AD do Azure para organizações híbridas
Organizações com uma infraestrutura de TI híbrida consomem uma combinação de recursos de nuvem e recursos locais. Essas organizações sincronizam informações de identidade de seu diretório local para seu locatário do Azure AD. Como as organizações híbridas buscam migrar mais de seus aplicativos locais para a nuvem, especialmente aplicativos herdados de reconhecimento de diretório, os Serviços de Domínio do AD do Azure podem ser muito úteis para eles.

A Litware Corporation implantou [o Azure AD Connect](../active-directory/active-directory-aadconnect.md) para sincronizar informações de identidade de seu diretório local para seu locatário do Azure AD. Isso inclui contas de usuário, seus hashes de credencial para autenticação (sincronização de senha) e associações de grupo. Observe que **a sincronização de senha é obrigatória para as organizações híbridas usarem os Serviços de Domínio do AD do Azure**. Isso ocorre porque as credenciais do usuário são necessárias no domínio gerenciado fornecido pelos Serviços de Domínio do AD do Azure para autenticar esses usuários com métodos de autenticação NTLM ou Kerberos.

![Serviços de domínio do AD do Azure para a Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A ilustração acima mostra como as organizações com uma infraestrutura de TI híbrida, como a Litware Corporation, podem usar os Serviços de Domínio do AD do Azure. Os aplicativos e cargas de trabalho de servidor da Litware que exigem serviços de domínio são implantados em uma rede virtual nos Serviços de Infraestrutura do Azure. O administrador de TI da Litware pode habilitar os Serviços de Domínio do AD do Azure para seu locatário do Azure AD e optar por disponibilizar um domínio gerenciado nessa rede virtual. Como a Litware é uma organização com uma infraestrutura de TI híbrida, credenciais, grupos e contas de usuário são sincronizados com seu locatário Azure AD de seu diretório local. Esse recurso permite aos usuários entrar no domínio usando suas credenciais corporativas, por exemplo, quando se conectam remotamente a computadores ingressados no domínio pela área de trabalho remota. Os administradores podem provisionar acesso a recursos no domínio usando associações a grupos existentes. Aplicativos implantados em máquinas virtuais na rede virtual se beneficiam de serviços de domínio como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos, política de grupo, etc.

Alguns aspectos de destaque do domínio gerenciado que é provisionado pelos Serviços de Domínio do AD do Azure são os seguintes:

- Ele é um domínio gerenciado autônomo. Não é uma extensão do domínio local da Litware.
- O administrador de TI da Litware não precisa gerenciar, fazer patch ou monitorar esse domínio ou qualquer controlador de domínio para o domínio gerenciado.
- Não é necessário gerenciar a replicação do AD para este domínio. Contas de usuário, associações de grupo e as credenciais do diretório local da Litware são sincronizados com o Azure AD usando o Azure AD Connect. Eles ficam automaticamente disponíveis nesse domínio gerenciado.
- Uma vez que o domínio é gerenciado pelos Serviços de Domínio do Azure AD, o administrador de TI da Litware não tem privilégios de administrador corporativo ou de administrador de domínio nesse domínio.


## Benefícios
Com os serviços de domínio do AD do Azure, você pode aproveitar os seguintes benefícios:

-	**Simples** – Você pode atender às necessidades de identidade de máquinas virtuais implantadas nos serviços de Infraestrutura do Azure com alguns cliques, sem a necessidade de implantar e gerenciar a infraestrutura de identidade no Azure ou configurar a conectividade para sua infraestrutura de identidade local.

-	**Integrados** – Os Serviços de Domínio do Azure AD são profundamente integrados ao seu locatário do Azure AD. Agora, você pode contar com o Azure AD para ser um diretório corporativo integrado baseado em nuvem que atende às necessidades tanto de seus aplicativos modernos, como aplicativos com reconhecimento de diretório tradicionais.

-	**Compatíveis** – Como os Serviços de Domínio do Azure AD se baseiam na infraestrutura de nível corporativo comprovada do Active Directory do Windows Server, os aplicativos podem depender de um maior grau de compatibilidade com os recursos do Active Directory do Windows Server. Observe que nem todos os recursos disponíveis no AD do Windows Server estão atualmente disponíveis nos Serviços de Domínio do AD do Azure. No entanto, os recursos disponíveis são compatíveis com os recursos correspondentes do AD do Windows Server que se baseiam na sua infraestrutura local. LDAP, Kerberos, NTLM, política de grupo e recursos de ingresso no domínio fornecidos pelos Serviços de Domínio do AD do Azure constituem uma oferta madura que foi testada e refinada em várias versões do Windows Server.

-	**Econômicos** – Com os Serviços de Domínio do Azure AD, você pode evitar a sobrecarga de gerenciamento e infraestrutura associada ao gerenciamento de infraestrutura de identidade para dar suporte a aplicativos tradicionais com reconhecimento de diretório. Você pode mover esses aplicativos para os Serviços de Infraestrutura do Azure e aproveitar economias ainda maiores em despesas operacionais.

<!---HONumber=AcomDC_1217_2015-->