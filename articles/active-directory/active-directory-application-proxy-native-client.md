<properties
	pageTitle="Como habilitar a publicação de aplicativos clientes nativos com aplicativos de proxy | Microsoft Azure"
	description="Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="kgremban"/>

# Como habilitar aplicativos clientes nativos para interagir com aplicativos de proxy
O Proxy de Aplicativo Active Directory do Azure é amplamente usado para publicar os aplicativos de navegador, como SharePoint, Outlook Web Access e linha personalizada de aplicativos comerciais. Ele também pode ser usado para publicar aplicativos HTTP de back-end que são consumidos usando clientes nativos. Isso é feito com suporte a tokens emitidos pelo Azure AD que são enviados em cabeçalhos HTTP Authorize padrão.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


O método recomendado para publicar aplicativos é usar a Biblioteca de Autenticação do AD do Azure que se encarrega de todo o incômodo da autenticação e dá suporte a muitos ambientes de cliente diferentes. O Proxy de Aplicativo se encaixa no [cenário de Aplicativo Nativo para API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). O processo para realizar isso é o seguinte:

1. Publique seu aplicativo proxy como faria com qualquer outro aplicativo, atribua usuários e dê a eles licenças basic ou premium. Para saber mais, consulte [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).
2. Configure seu aplicativo nativo da seguinte maneira:
  3. Entre no Portal de Gerenciamento do Azure.
  4. Clique no ícone do Active Directory no menu esquerdo e clique no diretório desejado.
  5. No menu superior, clique em Aplicativos. Se nenhum aplicativo tiver sido adicionado ao diretório, essa página mostrará apenas o link Adicionar um Aplicativo. Clique no link ou, se preferir, você pode clicar no botão Adicionar, na barra de comandos.
  4. Na página **O que você deseja fazer**, clique no link **Adicionar um aplicativo que minha organização esteja desenvolvendo**.
  5. Na página **Conte-nos sobre seu aplicativo**, especifique um nome para seu aplicativo e escolha **aplicativo cliente nativo**, que representa um aplicativo instalado em um dispositivo como telefone ou computador. Quando terminar, clique no ícone de seta no canto inferior direito da página.
  6. Na página **Propriedades do aplicativo**, forneça o **URI de redirecionamento** para o aplicativo cliente nativo e clique na caixa de seleção no canto inferior direito da página. </br>O aplicativo foi adicionado e você será levado para a página Início Rápido do seu aplicativo.
8. Habilite o aplicativo nativo para ser exposto a outros aplicativos no seu diretório:
  9. No menu superior, clique em **Aplicativos**, selecione o novo aplicativo nativo e clique em **Configurar**.
  10. Role para baixo até a seção **permissões para outros aplicativos**. Clique no botão **Adicionar aplicativo**, selecione o aplicativo de proxy ao qual você deseja conceder acesso de aplicativo nativo e clique na marca de seleção no canto inferior direito. No menu suspenso **Permissões Delegadas**, selecione a nova permissão. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4. Edite o código de aplicativo nativo no contexto de autenticação da ADAL (Biblioteca de Autenticação do Active Directory) para incluir o seguinte:

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

As variáveis devem ser substituídas da seguinte maneira:


- **ID do locatário** pode ser encontrada no GUID na URL da página **Configuração** do aplicativo, logo após " Directory/".
- **URL de front-end** é a URL de front-end inserida no Aplicativo de Proxy e pode ser encontrada na página **Configuração** do aplicativo proxy.
- A **Id do cliente** do aplicativo nativo pode ser encontrada na página **Configurar** do aplicativo nativo.
- O **URI de redirecionamento do aplicativo nativo** podem ser encontrado na página **Configurar** do aplicativo nativo.

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>Para saber mais sobre o fluxo do aplicativo nativo, consulte [Aplicativo nativo para API Web](active-directory-authentication-scenarios.md#native-application-to-web-api).






## O que vem a seguir?
Você pode fazer muito mais com o Proxy de Aplicativo:


- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)


### Saiba mais sobre o Proxy de Aplicativo
- [Veja nossa ajuda online](active-directory-application-proxy-enable.md)
- [Confira o blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
- [Assista aos nossos vídeos no Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionais
* [Inscrever-se no Azure como uma organização](sign-up-organization.md)
* [Identidade do Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_1125_2015-->