<properties 
	pageTitle="Autenticar e autorizar os apicativos de llinha de negócio em sites do Azure" 
	description="Aprenda as diferentes opções de autenticação e autorização para aplicativos de linha de negócios que são implantados em sites do Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Autenticar e autorizar usuários em aplicativos de linha de negócios em sites do Azure #

[Os Sites do Azure](http://azure.microsoft.com/pt-br/services/websites/) permitem cenários de aplicativos de linha de negócios da empresa oferecendo suporte a logon único (SSO) de usuários se eles acessarem o aplicativo do seu ambiente local ou na internet pública. Ele pode ser integrado com [Azure Active Directory](http://azure.microsoft.com/pt-br/services/active-directory/) (AAD) ou no local seguro serviço de token (STS), como os serviços de Federação do Active Directory (AD FS) para autenticar seus usuários internos do Active Directory (AD) e autorizá-los corretamente.

## Autorização e autenticação de conflito zero ##

Com alguns cliques do botão, você pode habilitar a autenticação e autorização para seu site. A configuração de estilo de caixa de seleção em cada site do Azure fornece o controle de acesso básico do seu site LOB. Ela faz isso aplicando HTTPS e autenticação para um locatário do AD do Azure de sua escolha antes de conceder aos usuários acesso a todo o conteúdo do site. Para obter mais informações, consulte [Autenticação/autorização dos sites do Azure](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Esse recurso está atualmente na visualização.

## Manualmente, implemente autenticação e autorização ##

Em muitos cenários, convém personalizar o comportamento de autenticação e autorização do aplicativo, como uma página de logon e logoff, lógica de autorização personalizada, comportamento do aplicativo de locatários múltiplos e assim por diante. Nesses casos, talvez seja melhor configurar a autenticação e a autorização manualmente para obter maior flexibilidade de seus recursos. Veja a seguir duas opções principais  

-	[AD do Azure](../web-sites-dotnet-lob-application-azure-ad/)  - você pode implementar autenticação e autorização para seu site com o AD do Azure. Usando o AD do Azure como o provedor de identidade tem as seguintes características:
	-	Oferece suporte a protocolos de autenticação populares como [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/), e [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Para obter a lista completa dos protocolos suportados, consulte [protocolos de autenticação do Active Directory do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn151124.aspx).
	-	Pode usar um provedor de identidade somente no Azure sem nenhuma infraestrutura local.
	-	Também é possível configurar a sincronização de diretórios com um AD no local (gerenciado no local).
	-	O AD do Azure com a sincronização de diretório do seu domínio do AD local permite uma experiência de SSO suave ao seu site quando os usuários do AD acessar da intranet e da internet. Da intranet, os usuários do AD podem acessar automaticamente o site por meio da autenticação integrada. Na internet, os usuários do AD podem fazer logon no site usando suas credenciais do Windows.
	-	Forneça o SSO para [todos os aplicativos suportados pelo AD do Azure](http://azure.microsoft.com/pt-br/marketplace/active-directory/), incluindo Azure, Office 365, Dynamics CRM Online, Windows InTune e milhares de aplicativos em nuvem que não são da Microsoft. 
	-	O AD do Azure delega o gerenciamento de aplicativos [de terceiros confiáveis](http://en.wikipedia.org/wiki/Relying_party) para funções de não-administrador, enquanto o aplicativo acessa dados de diretório confidenciais que ainda devem ser configurados pelos administradores globais.
	-	Envie um conjunto de finalidade geral de tipos de declaração para todos os aplicativos confiáveis. Para a lista de tipos de declarações, consulte [Token com suporte e tipos de declaração](http://msdn.microsoft.com/pt-br/library/azure/dn195587.aspx). As declarações não são personalizáveis.
	-	[O Azure AD Graph API](http://msdn.microsoft.com/pt-br/library/azure/hh974476.aspx) permite acesso do aplicativo aos dados do diretório no AD do Azure.
-	[Serviço de token seguro no local (STS), como o AD FS](../web-sites-dotnet-lob-application-adfs/) - você pode implementar a autenticação e a autorização para seu site com um STS local como o AD FS. Usar o AD FS no local tem as seguintes características:
	-	A topologia do AD FS deve ser implantada no local, com sobrecarga de gerenciamento e custo.
	-	Melhor quando a política da empresa exige que os dados do AD sejam armazenados no local.
	-	Apenas administradores do AD FS podem configurar [relações de confiança de terceiros e regras de declaração](http://technet.microsoft.com/pt-br/library/dd807108.aspx).
	-	Pode gerenciar [declarações](http://technet.microsoft.com/pt-br/library/ee913571.aspx) em uma base por aplicativo.
	-	Deve ter uma solução separada para acessar dados do AD local através do firewall corporativo.


<!--HONumber=42-->
