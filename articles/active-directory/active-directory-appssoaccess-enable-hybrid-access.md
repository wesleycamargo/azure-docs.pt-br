<properties
	pageTitle="Habilitando o acesso híbrido com Proxy de Aplicativo| Microsoft Azure"
	description="Habilite o acesso a aplicativos em execução na sua rede privada de fora da sua rede pelo Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#Habilitando o acesso híbrido com Proxy de Aplicativo
Com o Proxy de Aplicativo do AD do Microsoft Azure, você pode publicar os aplicativos em execução na sua rede privada para que eles possam ser acessados com segurança, em qualquer dispositivo, de fora da sua rede. O Proxy de Aplicativo do Azure AD pode ser usado para publicar aplicativos na rede local privada ou na nuvem. Ele pode ser configurado como um serviço de caixa no Azure AD e somente requer a instalação de um conector de proxy de aplicativo em um servidor no seu ambiente.

##Como ele funciona

As etapas a seguir listam como os aplicativos podem ser publicados em um ambiente híbrido.
 
1.	Os conectores são implantados na rede corporativa. (Vários conectores podem ser implantados para redundância e escala.)
2.	O conector se conecta automaticamente ao serviço de nuvem.
3.	O usuário se conecta ao serviço de nuvem que direciona o tráfego para os recursos por meio de conectores.

O diagrama a seguir fornece mais detalhes sobre o funcionamento do Proxy de Aplicativo:

 ![Como funciona o Proxy de Aplicativo do AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	O usuário acessa o Aplicativo usando o Proxy de Aplicativo e é direcionado para a página de logon do Azure AD para autenticar.
2.	Depois do logon bem-sucedido, um token é gerado e enviado para o usuário.
3.	O usuário agora enviará o token para o Proxy de Aplicativo que irá recuperar o nome de usuário principal (UPN) e o nome da entidade de segurança (SPN) do token e direcionar a solicitação para o conector.
4.	O conector representa o usuário para solicitar um tíquete Kerberos que pode ser usado para autenticação interna (Windows). (Delegação Restrita de Kerberos)
5.	 Um tíquete Kerberos é recuperado do Active Directory.
6.	O tíquete recuperado é enviado para o servidor de aplicativos para verificação.
7.	 A resposta será enviada através do Proxy de Aplicativo para o usuário final. Há apenas um requisito para publicar um aplicativo Web: o aplicativo Web na sua rede privada deve poder ser acessado pelo servidor onde o conector está sendo instalado. Em outras palavras, você pode instalar o conector nos próprios servidores de aplicativo ou em qualquer outro servidor no seu ambiente. Não é problema, desde que o conector seja capaz de acessar o aplicativo Web.

##Como integrar os aplicativos locais usando o Proxy de Aplicativo do Azure AD
O Painel de Acesso no Azure AD permite a você publicar seus aplicativos locais no Azure AD. Você deve seguir as etapas abaixo:

1. Habilitar o Proxy de Aplicativo no Azure AD, instalar e registrar o conector. Para obter instruções detalhadas, confira [Proxy de Aplicativo do Azure AD](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md).
2. Publicar aplicativos usando o Proxy de Aplicativo do Azure AD: para obter instruções detalhadas, confira [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](active-directory-application-proxy-publish.md).

<!---HONumber=Oct15_HO2-->