<properties
	pageTitle="Habilitando o acesso híbrido com Proxy de Aplicativo| Microsoft Azure"
	description="Habilite o acesso a aplicativos em execução na sua rede privada de fora da sua rede pelo Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	keywords="acesso de aplicativo, Proxy de aplicativo, acesso híbrido"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# Habilitando o acesso híbrido com Proxy de Aplicativo
Com o Proxy de Aplicativo do Active Directory do Microsoft Azure (AD), você pode fornecer acesso a aplicativos localizados em sua rede privada de forma segura, de qualquer lugar e em qualquer dispositivo. Depois de instalar um conector de proxy de aplicativo em seu ambiente, ele pode ser facilmente configurado com o Azure AD.

Há apenas um requisito para habilitar o acesso a um aplicativo Web: o aplicativo Web deve ser acessível para o servidor onde o conector está instalado. Em outras palavras, você pode instalar o conector no próprio servidor de aplicativo ou em qualquer outro servidor no seu ambiente, contanto que o conector consiga acessar o aplicativo Web.

##Como ele funciona
### Visão geral
1. Os conectores são implantados na rede local. (Vários conectores podem ser implantados para redundância e escala.)
2. O conector se conecta automaticamente ao serviço de nuvem.
3. O conector e o serviço de nuvem roteiam o tráfego do usuário para os aplicativos.

 ![Diagrama do Proxy de Aplicativo do AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### Uma análise mais detalhada
1. O usuário acessa o aplicativo usando o proxy de aplicativo e será direcionado para a página de logon do AD do Azure para autenticar.
2. Depois do logon bem-sucedido, um token é gerado e enviado para o usuário.
3. O usuário agora envia o token para o proxy de aplicativo que irá recuperar o nome de usuário principal (UPN) e o nome da entidade de segurança (SPN) do token e direcionar a solicitação para o conector.
4. Em nome do usuário, o conector solicita um tíquete Kerberos que pode ser usado para a autenticação (Windows) interna. Isso é conhecido como Delegação Restrita de Kerberos.
5. Um tíquete Kerberos é recuperado do Active Directory.
6. A permissão é enviada para o servidor de aplicativo e verificada.
7. A resposta é enviada através do proxy de aplicativo para o usuário.

## Artigos relacionados
- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Habilitando o Proxy de Aplicativo do AD do Azure.](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Publicar aplicativos por meio do proxy de aplicativo do AD do Azure](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0218_2016-->