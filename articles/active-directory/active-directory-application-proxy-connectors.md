<properties
	pageTitle="Trabalhando com Conectores de Proxy de Aplicativo do Azure AD"
	description="Aborda como criar e gerenciar grupos de Conectores no Proxy de Aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="rkarlin"/>


# Publicar aplicativos em redes e locais separados usando grupos de Conectores

> [AZURE.NOTE]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-edition.md).

Grupos de Conectores são úteis para uma série de situações, incluindo:


- Sites com vários datacenters interconectados, nos quais você deseja manter o tráfego dentro do datacenter o máximo possível e não utilizar links entre datacenters, que geralmente são mais caros e têm alta latência. Você pode implantar Conectores em cada datacenter para servir apenas os aplicativos que residem no datacenter, e assim salvar valiosos links entre datacenters e fornecer uma experiência totalmente transparente para os usuários.
- Gerenciar aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de Conectores para instalar Conectores dedicados para redes isoladas para permitir também o isolamento de aplicativos para a rede.
- Para aplicativos instalados no IaaS para o acesso à nuvem, grupos de Conectores fornecem um serviço comum para proteger o acesso a todos eles sem criar dependência adicional em sua rede corporativa ou fragmentação da experiência. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários Conectores para obter alta disponibilidade.
- Suporte para ambientes de várias florestas em que Conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
- Grupos de Conectores podem ser usados em sites de Recuperação de desastres para detectar failover ou como backup do site principal.
- Grupos de Conectores também podem ser usados para atender a várias empresas de um único locatário.


## Trabalhando com grupos de Conectores
Para agrupar seus Conectores, você precisa assegurar-se de ter [instalado vários Conectores](active-directory-application-proxy-enable.md) e de tê-los nomeado e agrupado. Por fim, você precisa atribuí-los a aplicativos específicos.

## Etapa 1: Criar grupos de Conectores
Você pode criar quantos grupos de Conectores desejar. A criação de grupo de Conectores é feita no portal do Azure. Selecione o diretório e clique em **Configurar**. Depois, em Proxy de Aplicativo, clique em **Gerenciar Grupos de Conectores** e crie um novo grupo de Conectores fornecendo um nome ao grupo: ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png) ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)
## Etapa 2: Atribuir Conectores aos seus grupos
Após a criação de grupos de Conectores, mova os Conectores para o grupo apropriado. Em **Proxy de Aplicativo**, clique em **Gerenciar Conectores** e, em **Grupo**, selecione o grupo desejado para cada Conector. Observe que podem ser necessários até 10 minutos para que os Conectores fiquem ativos no novo grupo. ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)
## Etapa 3: Atribuir aplicativos aos grupos de Conectores
A última etapa é atribuir cada aplicativo ao grupo de Conectores que vai servi-lo. No portal do Azure, no seu diretório, selecione o Aplicativo que você deseja atribuir ao grupo e clique em **Configurar**. Em **Grupo de conectores**, selecione o grupo que deseja usar. A alteração é aplicada imediatamente. ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png) Para obter mais informações sobre publicação de aplicativos, consulte [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
## Consulte também
Você pode fazer muito mais com o Proxy de Aplicativo:

- [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
- [Solucionar problemas que surgirem com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)

## Saiba mais sobre o Proxy de Aplicativo
- [Veja nossa ajuda online](active-directory-application-proxy-enable.md)
- [Confira o blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
- [Assista aos nossos vídeos no Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionais

* [Sobre a delegação restrita de Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=Oct15_HO3-->