<properties 
	pageTitle="Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure" 
	description="Aprenda as diferentes opções de autenticação e autorização para aplicativos de linha de negócios que são implantados em aplicativos Web do Serviço de Aplicativo do Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="07/02/2015" 
	ms.author="cephalin"/>

# Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure #

[Aplicativos Web de Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) habilitam cenários de aplicativos na linha de negócios da empresa, dando suporte a SSO (logon único) de usuários dependendo de eles acessarem o aplicativo do seu ambiente local ou da Internet pública. Eles podem ser integrados ao AAD ([Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)) ou a um STS (serviço de token local seguro), como o AD FS (Serviços de Federação do Active Directory), para autenticar os usuários internos do Active Directory e autorizá-los corretamente.

## Autenticação e autorização sem nenhum atrito ##

Com alguns cliques no botão, você pode habilitar a autenticação e autorização para seu aplicativo Web. A configuração de estilo de caixa de seleção em todo aplicativo Web do Azure fornece o controle de acesso básico do seu aplicativo Web de linha de negócios. Ela faz isso aplicando HTTPS e autenticação a um locatário do AD do Azure de sua escolha antes de conceder aos usuários acesso a todo o conteúdo do aplicativo Web. Para saber mais, consulte [Autenticação/autorização de aplicativos Web](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE]Esse recurso está atualmente na visualização.

## Implementar autenticação e autorização manualmente ##

Em muitos cenários, convém personalizar o comportamento de autenticação e autorização do aplicativo, assim como uma página de logon e logout, lógica de autorização personalizada, o comportamento do aplicativo multilocatário e assim por diante. Nesses casos, talvez seja melhor configurar a autenticação e autorização manualmente para obter maior flexibilidade de seus recursos. Veja a seguir duas opções principais

-	[AD do Azure](web-sites-dotnet-lob-application-azure-ad.md) -você pode implementar autenticação e autorização para seu aplicativo Web com o AD do Azure. O uso do AD do Azure como o provedor de identidade tem as seguintes características:
	-	Dá suporte a protocolos de autenticação populares, como [OAuth 2.0](http://oauth.net/2/), o [OpenID Connect](http://openid.net/connect/) e o [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Para obter a lista completa dos protocolos com suporte, consulte [Protocolos de autenticação do Active Directory do Azure](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	É possível usar um provedor de identidade exclusivamente para o Azure, sem nenhuma infraestrutura local.
	-	Também é possível configurar a sincronização de diretório com um AD local (gerenciado no local).
	-	O AD do Azure com a sincronização de diretório do domínio do AD local proporciona uma experiência tranquila de SSO para o aplicativo Web quando os usuários do AD o acessam por meio da intranet e da Internet. Na intranet, os usuários do AD podem acessar automaticamente o aplicativo Web por meio da autenticação integrada. Da Internet, os usuários do AD podem fazer logon no aplicativo Web usando suas credenciais do Windows.
	-	Fornece SSO [para todos os aplicativos com suporte do AD do Azure](/marketplace/active-directory/), incluindo Azure, Office 365, Dynamics CRM Online, o Microsoft Intune e milhares de aplicativos em nuvem que não são da Microsoft. 
	-	O AD do Azure delega o gerenciamento de aplicativos de [terceira parte confiável](http://en.wikipedia.org/wiki/Relying_party) para funções de não administrador, enquanto o acesso do aplicativo a dados de diretório confidenciais ainda deve ser configurado pelos administradores globais.
	-	Envia um conjunto de finalidade geral de tipos de declaração para todos os aplicativos de terceira parte confiável. Para a lista de tipos de declaração, consulte [Token com suporte e tipos de declaração](http://msdn.microsoft.com/library/azure/dn195587.aspx). Declarações não são personalizáveis.
	-	A [Graph API do AD do Azure](http://msdn.microsoft.com/library/azure/hh974476.aspx) habilita acesso do aplicativo aos dados de diretório no AD do Azure.
-	[STS (serviço seguro de token) local, como o AD FS](../web-sites-dotnet-lob-application-adfs/) — você pode implementar autenticação e autorização para o aplicativo Web com um STS local como o AD FS. O uso do AD FS local tem as seguintes características:
	-	A topologia do AD FS deve ser implantada no local, com sobrecarga de gerenciamento e de custo.
	-	É melhor quando a política da empresa exige que os dados do AD sejam armazenados localmente.
	-	Apenas administradores do AD FS podem configurar [regras de declaração e relações de confiança de terceiras partes confiáveis](http://technet.microsoft.com/library/dd807108.aspx).
	-	Pode gerenciar [declarações](http://technet.microsoft.com/library/ee913571.aspx) de acordo com o aplicativo.
	-	Deve ter uma solução separada para acessar dados do AD local através do firewall corporativo.

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO4-->