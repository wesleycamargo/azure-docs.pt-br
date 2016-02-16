<properties
	pageTitle="Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure, permitindo acesso apenas a usuários autenticados."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [seletor](../../includes/app-service-api-auth-selector.md)]

## Visão geral

Neste artigo, você aprenderá o seguinte:

* Como proteger um aplicativo de API do Serviço de Aplicativo para que somente usuários autenticados possam chamá-lo.
* Como configurar um provedor de autenticação, com detalhes para o Active Directory do Azure (Azure AD).
* Como consumir um aplicativo de API protegido usando a [ADAL (Biblioteca de Autenticação do Active Directory) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

O artigo contém duas seções:

* A seção [Como configurar a autenticação de usuário no Serviço de Aplicativo do Azure](#authconfig) explica em termos gerais como configurar a autenticação de usuário para qualquer aplicativo de API e aplica-se igualmente a todas as estruturas às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java.

* O [restante do artigo](#tutorialstart) o orienta ao longo da configuração de um aplicativo de exemplo do .NET em execução no Serviço de Aplicativo, para que ele use o Active Directory do Azure para autenticação do usuário.

## <a id="authconfig"></a> Como configurar a autenticação de usuário no Serviço de Aplicativo do Azure

Esta seção fornece instruções gerais que se aplicam a qualquer aplicativo de API. Para obter etapas específicas para o aplicativo de exemplo .NET de Lista de Tarefas Pendentes, vá para [Continuação dos tutoriais de introdução do .NET](#tutorialstart).

1. No [Portal do Azure](https://portal.azure.com/), navegue até a folha **Aplicativo de API** do aplicativo de API que você deseja proteger e clique em **Configurações**

2. Na folha **Configurações**, localize a seção **Recursos** e clique em **Autenticação/Autorização**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Selecione uma das opções da lista suspensa **Ação a ser tomada quando a solicitação não estiver autenticada**.

	* Se você quiser que apenas chamadas autenticadas cheguem ao aplicativo de API, escolha uma das opções de **Fazer logon com...**. Essa opção o habilita a proteger o aplicativo de API sem escrever código executado nele.

	* Se você desejar que todas as chamadas cheguem ao aplicativo de API, escolha **Permitir solicitação (nenhuma ação)**. Você pode usar essa opção para direcionar chamadores não autenticados para diversos provedores de autenticação. Com essa opção, você precisa escrever código para lidar com a autorização.

	Para obter mais informações, consulte [Autenticação e autorização para aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-authentication.md#multiple-protection-options).

5. Selecione um ou mais dos **Provedores de Autenticação**.

	A imagem mostra opções que exigem que todos os chamadores sejam autenticados pelo Azure AD.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Quando você escolher um provedor de autenticação, o portal exibirá uma folha de configuração para o provedor.

	Para obter instruções que explicam como configurar as folhas de configuração do provedor de autenticação, consulte [Como configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Active Directory do Azure](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (O link vai para um artigo sobre o Azure AD, mas o artigo em si contém guias com links para artigos referentes aos outros provedores de autenticação.)

7. Quando você terminar de usar a folha de configuração do provedor de autenticação, clique em **OK**.

7. Na folha **Autenticação/Autorização**, clique em **Salvar**.

Quando isso for feito, o Serviço de Aplicativo autenticará todas as chamadas de API antes que elas cheguem ao aplicativo de API. Os serviços de autenticação funcionam da mesma para todas as linguagens às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java.

Para fazer chamadas de API autenticadas, o chamador inclui o token de portador OAuth 2.0 do provedor de autenticação no cabeçalho de Autorização de solicitações HTTP. O token pode ser adquirido usando o SDK do provedor de autenticação.

## <a id="tutorialstart"></a> Continuação dos tutoriais de introdução do .NET

Se você estiver seguindo a série de introdução do Node.js ou do Java para aplicativos de API, vá para o próximo artigo, [Autenticação de entidade de serviço para aplicativos de API](app-service-api-dotnet-service-principal-auth.md).

Se você estiver seguindo a série de introdução do .NET para aplicativos de API e já tiver implantado o aplicativo de exemplo, conforme indicado no [primeiro](app-service-api-dotnet-get-started.md) e no [segundo](app-service-api-cors-consume-javascript.md) tutoriais, vá para a seção [Configurar a autenticação](#azureauth).

Se você ainda não realizou o primeiro e o segundo tutoriais e deseja seguir este, use o botão **Implantar no Azure** do [arquivo Leiame do repositório de exemplo de Lista de Tarefas Pendentes](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) para implantar os aplicativos de API e o aplicativo Web.

Quando a implantação for concluída, será mostrado um link HTTP para o aplicativo Web. Para executar o aplicativo e verificar se ele está funcionando, altere essa URL para HTTPS.

## <a id="azureauth"></a> Configurar a autenticação no Azure

Neste ponto, o aplicativo está em execução no Serviço de Aplicativo do Azure sem exigir que os usuários sejam autenticados. Nesta seção, você pode adicionar a autenticação realizando as seguintes tarefas:

* Configurar o Serviço de Aplicativo para exigir autenticação do Active Directory do Azure (Azure AD) para chamar o aplicativo de API de camada intermediária.
* Criar um aplicativo Azure AD.
* Configurar o aplicativo Azure AD para enviar o token de portador após o logon para o front-end do AngularJS. 

### Configurar a autenticação no Serviço de Aplicativo

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha **Aplicativo de API** do aplicativo de API que você criou para o projeto ToDoListAPI.

2. Clique em **Configurações**

2. Na folha **Configurações**, localize a seção **Recursos** e clique em **Autenticação/Autorização**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, escolha **Fazer logon com o Active Directory do Azure**.

	Essa opção garante que nenhuma solicitação não autenticada chegue ao aplicativo de API.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Com a opção **Expresso**, o Serviço de Aplicativo pode criar automaticamente um aplicativo Azure AD no [locatário](https://msdn.microsoft.com/pt-BR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) do Azure AD.

	Você não precisa criar um locatário, pois cada conta do Azure tem um automaticamente.

7. Em **Modo de gerenciamento**, clique em **Criar novo aplicativo do AD**.

	O portal insere um valor padrão na caixa de entrada **Criar Aplicativo**.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. Anote o valor que está na caixa de entrada **Criar Aplicativo**; você procurará esse aplicativo do AAD no portal clássico do Azure posteriormente.

	O Azure criará automaticamente um aplicativo Azure AD no locatário do Azure AD. Por padrão, o aplicativo Azure AD tem o mesmo nome que o aplicativo de API. Se preferir, você pode inserir um nome diferente.
 
7. Clique em **OK**.

7. Na folha **Autenticação/Autorização**, clique em **Salvar**.

Agora, somente os usuários em seu locatário do Azure AD podem chamar o aplicativo de API.

### Opcional: testar o aplicativo de API

1. Em um navegador, vá para a URL do aplicativo de API: na folha **Aplicativo de API** no portal do Azure, clique no link em **URL**.  

	Você será redirecionado para uma tela de logon, pois solicitações não autenticadas não têm permissão para chegar ao aplicativo de API.

	Se o navegador for para a página "criado com êxito", talvez ele já esteja conectado. Nesse caso, abra uma janela InPrivate ou Incognito e vá para a URL do aplicativo de API.

2. Faça logon usando as credenciais de um usuário no locatário do Azure AD.

	Quando você estiver conectado, uma página "criado com êxito" será exibida no navegador.

9. Feche o navegador.

### Configurar o aplicativo Azure AD

Quando você configurou a autenticação do Azure AD, o Serviço de Aplicativo criou um aplicativo Azure AD para você. Por padrão, o novo aplicativo Azure AD foi configurado para fornecer o token de portador à URL do aplicativo de API. Nesta seção, você configurará o aplicativo Azure AD para fornecer o token de portador ao front-end do AngularJS, em vez de diretamente ao aplicativo de API de camada intermediária. O front-end do AngularJS enviará o token ao aplicativo de API quando ele chamar o aplicativo de API.

11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

	Você precisa usar o portal clássico porque determinadas configurações do Active Directory do Azure às quais você precisa ter acesso ainda não estão disponíveis no portal do Azure atual.

12. Na guia **Diretório**, clique no locatário do AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

	Talvez também seja necessário atualizar a página para ver o novo aplicativo.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Clique em **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Defina a **URL de logon** como a URL do aplicativo Web do AngularJS, sem uma barra à direita.

	Por exemplo: https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Defina a **URL de resposta** como a URL do aplicativo Web, sem uma barra à direita.

	Por exemplo: https://todolistsangular.azurewebsites.net

16. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Na parte inferior da página, clique em **Gerenciar manifesto > Baixar manifesto**.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Baixe o arquivo para um local em que você possa editá-lo.

16. No arquivo de manifesto baixado, procure a propriedade `oauth2AllowImplicitFlow`. Altere o valor dessa propriedade de `false` para `true` e salve o arquivo.

	Essa configuração é necessária para o acesso de um aplicativo de página única de JavaScript. Ele habilita o token de portador Oauth 2.0 a ser retornado no fragmento de URL.

16. Clique em **Gerenciar manifesto > Carregar manifesto** e carregue o arquivo atualizado na etapa anterior.

17. Copie o valor de **ID do cliente** e salve-o em algum local em que você possa obtê-lo posteriormente.

## Configure o projeto ToDoListAngular para usar a autenticação

Nesta seção, você alterará o front-end do AngularJS para que ele use a ADAL (Biblioteca de Autenticação do Active Directory) para JS a fim de adquirir um token de portador para o usuário conectado do Azure AD. O código incluirá o token em solicitações HTTP enviadas à camada intermediária, como mostrado no diagrama a seguir.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Faça as alterações a seguir em arquivos no projeto ToDoListAngular.

1. Abra o arquivo *index.html*.

2. Remova as marcas de comentários das linhas que fazem referência à ADAL (Biblioteca de Autenticação do Active Directory) para scripts JS.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. Abra o arquivo *app/scripts/app.js*.

2. Comente o bloco de código marcado para "sem autenticação" e remova as marcas de comentários do bloco de código marcado para "com autenticação".

	Essa alteração referencia o provedor de autenticação ADAL JS e fornece valores de configuração a ele. Nas etapas a seguir, você definirá os valores de configuração para o aplicativo de API e o aplicativo Azure AD.

8. No código que define a variável `endpoints`, defina a URL da API como a URL do aplicativo de API que você criou para o projeto ToDoListAPI e defina a ID de aplicativo Azure AD como a ID do cliente que você copiou do portal clássico do Azure.

	Agora, o código é semelhante ao exemplo a seguir.

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Na chamada para `adalProvider.init`, defina `tenant` com o nome de seu locatário e `clientId` com o mesmo valor usado na etapa anterior.

	Agora, o código é semelhante ao exemplo a seguir.

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

	Essas alterações para `app.js` especificam que o código de chamada e a API chamada estão no mesmo aplicativo Azure AD.

1. Abra o arquivo *app/scripts/homeCtrl.js*.

2. Comente o bloco de código marcado para "sem autenticação" e remova as marcas de comentários do bloco de código marcado para "com autenticação".

1. Abra o arquivo *app/scripts/indexCtrl.js*.

2. Comente o bloco de código marcado para "sem autenticação" e remova as marcas de comentários do bloco de código marcado para "com autenticação".

### Implante o projeto ToDoListAngular no Azure.

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAngular e em **Publicar**.

9. Clique em **Publicar**.

	O Visual Studio implanta o projeto e abre a URL base do aplicativo Web em um navegador.

	Você ainda precisa fazer uma alteração no aplicativo de API de camada intermediária antes de testar o aplicativo.

10. Feche o navegador.

## Configurar o projeto ToDoListAPI para usar a autenticação

Atualmente, o projeto ToDoListAPI envia "*" como o valor `owner` para ToDoListDataAPI. Nesta seção, você alterará o código para enviar a ID do usuário conectado.

Faça as alterações a seguir no projeto ToDoListAPI.

1. Abra o arquivo *Controllers/ToDoListController.cs* e remova a marca de comentário da linha em cada método de ação que define `owner` com o valor de declaração `NameIdentifier` do Azure AD. Por exemplo:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Implantar o projeto ToDoListAPI no Azure

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAPI e clique em **Publicar**.

9. Clique em **Publicar**.

	O Visual Studio implanta o projeto e abre a URL base do aplicativo de API em um navegador.

10. Feche o navegador.

### Testar o aplicativo

9. Vá para a URL do aplicativo Web, **usando HTTPS, não HTTP**.

8. Clique na guia **Lista de tarefas pendentes**.

	Você receberá uma solicitação para fazer logon.

9. Faça logon com as credenciais de um usuário em seu locatário do AAD.

10. A página **Lista de tarefas pendentes** será exibida.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	Não são exibidos itens pendentes porque, até agora, todos eles eram para o proprietário "*". Agora, a camada intermediária está solicitando itens para o usuário conectado, e nenhum foi criado ainda.

11. Adicione novos itens pendentes para verificar se o aplicativo está funcionando.

12. Em outra janela do navegador, vá para a URL da interface do usuário do Swagger para o aplicativo de API ToDoListDataAPI e clique em **ToDoList > Get**. Digite um asterisco para o parâmetro `owner` e clique em **Experimentar**.

	A resposta mostra que os novos itens pendentes têm a ID de usuário do Azure AD na propriedade Owner.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Como criar os projetos do zero

Os dois projetos de API Web foram criados usando o modelo de projeto **Aplicativo de API do Azure** e substituindo o controlador de Valores padrão por um controlador ToDoList.

Para obter informações sobre como criar um aplicativo de página única do AngularJS com um back-end de API Web 2, consulte [Laboratório prático: criar um SPA (aplicativo de página única) com a API Web do ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obter informações sobre como adicionar código de autenticação do Azure AD, confira os seguintes recursos:

* [Como proteger aplicativos de página única AngularJS com o Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Apresentação da ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Solucionar problemas

Se você executar com êxito o aplicativo sem autenticação e, depois, ele não funcionar quando você implementar a autenticação, na maioria das vezes o problema será decorrente de configurações incorretas ou inconsistentes. Comece verificando todas as configurações no Serviço de Aplicativo do Azure e no Active Directory do Azure. Aqui estão algumas sugestões específicas:

* Na guia **Configurar** do Azure AD, verifique novamente a **URL de resposta**.
* No Azure AD, baixe o manifesto e verifique se `oauth2AllowImplicitFlow` foi alterado com êxito para `true`. 
* No código-fonte do AngularJS, verifique novamente a URL do aplicativo de API de camada intermediária e a ID de cliente do Azure AD.
* Depois de configurar o código em um projeto, verifique se você reimplantou esse projeto e não um dos outros.
* Verifique se você está usando URLs HTTPS no navegador, não URLs HTTP.
* Verifique se CORS ainda está habilitado no aplicativo de API de camada intermediária, permitindo chamadas à camada intermediária por meio da URL HTTPS do front-end. Caso esteja em dúvida se o problema está relacionado ao CORS, tente usar "*" como a URL de origem permitida.
* Obtenha o máximo possível de informações nas mensagens de erro definindo o [modo customErrors como Desativado](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* A guia Console de Ferramentas de Desenvolvedor do navegador geralmente tem mais informações de erro, e você pode examinar as solicitações HTTP na guia Rede.

## Próximas etapas

Neste tutorial, você aprendeu como usar a autenticação do Serviço de Aplicativo para um aplicativo de API e como chamar o aplicativo de API usando a biblioteca ADAL JS. No próximo tutorial, você aprenderá a [proteger o acesso ao aplicativo de API para cenários entre serviços](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0211_2016-->