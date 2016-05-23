<properties
	pageTitle="Trabalhando com Conectores de Proxy de Aplicativo do AD do Azure | Microsoft Azure"
	description="Aborda como criar e gerenciar grupos de Conectores no Proxy de Aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>


# Publicar aplicativos em redes e locais separados usando grupos de Conectores

> [AZURE.NOTE] O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

Grupos de conectores são úteis para uma série de cenários, incluindo:


- Sites com vários datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters normalmente são caros e lentos. Você pode implantar Conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.
- Gerenciar aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de Conectores para instalar Conectores dedicados em redes isoladas para isolar também os aplicativos para a rede.
- Para aplicativos instalados no IaaS para o acesso à nuvem, grupos de Conectores fornecem um serviço comum para proteger o acesso a todos os aplicativos sem criar dependência adicional em sua rede corporativa ou fragmentar a experiência. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários Conectores para obter alta disponibilidade.
- Suporte para ambientes de várias florestas em que Conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
- Grupos de Conectores podem ser usados em sites de Recuperação de desastres para detectar failover ou como backup do site principal.
- Grupos de Conectores também podem ser usados para atender a várias empresas de um único locatário.

## Pré-requisito: Criar seus Conectores
Para agrupar seus Conectores, você precisa assegurar-se de ter [instalado vários Conectores](active-directory-application-proxy-enable.md) e de tê-los nomeado e agrupado. Por fim, você precisa atribuí-los a aplicativos específicos.

## Etapa 1: Criar grupos de Conectores
Você pode criar quantos grupos de Conectores desejar. A criação de grupo de Conectores é feita no portal clássico do Azure.

1. Selecione o diretório e clique em **Configurar**. ![Captura de tela de configuração do proxy de aplicativo - clique em gerenciar grupos de conectores](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Em Proxy de Aplicativo, clique em **Gerenciar Grupos de Conectores** e crie um novo grupo de Conectores fornecendo um nome ao grupo. ![Captura de tela de grupos de conectores de proxy de aplicativo - nomeie o novo grupo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Etapa 2: Atribuir Conectores aos seus grupos
Após a criação dos grupos de Conectores, mova os Conectores para o grupo apropriado.

1. Em **Proxy de Aplicativo**, clique em **Gerenciar Conectores**.
2. Em **Grupo**, selecione o grupo desejado para cada Conector. Observe que podem ser necessários até 10 minutos para que os Conectores fiquem ativos no novo grupo. ![Captura de tela de conectores de proxy de aplicativo - selecione o grupo do menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Etapa 3: Atribuir aplicativos aos grupos de Conectores
A última etapa é atribuir cada aplicativo ao grupo de Conectores que vai servi-lo.

1. No portal clássico do Azure, no seu diretório, selecione o Aplicativo que você deseja atribuir ao grupo e clique em **Configurar**.
2. Em **Grupo de conectores**, selecione o grupo que deseja usar. A alteração é aplicada imediatamente. ![Captura de tela de grupo de conectores de proxy de aplicativo - selecione o grupo do menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## Consulte também

- [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

Para ver as últimas notícias e atualizações, confira o [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0511_2016-->