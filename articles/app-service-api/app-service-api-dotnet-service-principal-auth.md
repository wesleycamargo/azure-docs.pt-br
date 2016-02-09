<properties
	pageTitle="Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure em cenários serviço a serviço."
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
	ms.topic="get-started-article"
	ms.date="01/26/2016" 
	ms.author="tdykstra"/>

# Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-auth-selector.md)]

## Visão geral

Neste artigo, você aprenderá o seguinte:

* Como usar o Active Directory do Azure (Azure AD) para proteger um aplicativo de API contra acesso não autenticado.
* Como consumir um aplicativo de API protegido usando as credenciais da entidade de serviço (identidade de aplicativo).
* Como verificar se o aplicativo de API protegido não pode ser chamado de um navegador por usuários conectados.
* Como verificar se o aplicativo de API protegido só pode ser chamado por uma entidade de serviço do Azure AD específica.

Esse método de proteção de um aplicativo de API normalmente é usado para [cenários internos](app-service-api-authentication.md#internal), como para chamar de um aplicativo de API para outro aplicativo de API.

O artigo contém duas seções:

* A seção [Como configurar a autenticação de entidade de serviço no Serviço de Aplicativo do Azure](#authconfig) explica em termos gerais como configurar a autenticação para qualquer aplicativo de API e como consumir o aplicativo de API protegido. Esta seção aplica-se igualmente a todas as estruturas às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java.

* O [restante do artigo](#tutorialstart) o orienta ao longo da configuração de um cenário de "acesso interno" para um aplicativo de exemplo .NET em execução no Serviço de Aplicativo.

## <a id="authconfig"></a> Como configurar a autenticação da entidade de serviço no Serviço de Aplicativo do Azure

Esta seção fornece instruções gerais que se aplicam a qualquer aplicativo de API. Para obter etapas específicas para o aplicativo de exemplo .NET de Lista de Tarefas Pendentes, vá para [Continuação dos tutoriais de introdução do .NET](#tutorialstart).

1. No [Portal do Azure](https://portal.azure.com/), navegue até a folha **Aplicativo de API** do aplicativo de API que você deseja proteger e clique em **Configurações**

2. Na folha **Configurações**, localize a seção **Recursos** e clique em **Autenticação/Autorização**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a ser realizada quando a solicitação não está autenticada**, selecione **Fazer logon com o Active Directory do Azure**.

5. Em **Provedores de Autenticação**, selecione **Active Directory do Azure**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configure a folha **Configurações do Active Directory do Azure** para criar um novo aplicativo Azure AD ou use um aplicativo existente do Azure AD, se você já tiver um que deseje usar.

	Cenários internos normalmente envolvem um aplicativo de API que chama um aplicativo de API. Você pode usar aplicativos do Azure AD separados para cada aplicativo de API ou apenas um aplicativo Azure AD.

	Para obter instruções detalhadas sobre essa folha, confira [Como configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Active Directory do Azure](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Quando você terminar de usar a folha de configuração do provedor de autenticação, clique em **OK**.

7. Na folha **Autenticação/Autorização**, clique em **Salvar**.

Quando isso for feito, o Serviço de Aplicativo impedirá que qualquer chamada de API não autenticada acesse o aplicativo de API. Nenhum código de autenticação ou de autorização é necessário no aplicativo de API protegido.

Essa funcionalidade de autenticação funciona da mesma maneira para todas as linguagens às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java.

#### Como consumir o aplicativo de API protegido

O chamador deve fornecer um token de portador do Azure AD com chamadas de API. Para obter um token de portador usando credenciais de entidade de serviço, o chamador usa a ADAL (Biblioteca de Autenticação do Active Directory para [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs), ou [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Para obter um token, o código que chama a ADAL fornece a ela as seguintes informações:

* O nome de seu locatário do Azure AD.
* A ID e o segredo do cliente (chave do aplicativo) do aplicativo Azure AD associado ao chamador.
* A ID do cliente do aplicativo Azure AD associado ao aplicativo de API protegido. (Se apenas um aplicativo Azure AD for usado, essa será a mesma ID de cliente usada para o chamador.)

Esses valores estão disponíveis nas páginas do Azure AD do [portal clássico do Azure](https://manage.windowsazure.com/).

Depois que o token for adquirido, o chamador o incluirá em solicitações HTTP no cabeçalho de Autorização. O Serviço de Aplicativo valida o token e permite que as solicitações cheguem ao aplicativo de API protegido.

#### Como proteger o aplicativo de API contra acesso por usuários no mesmo locatário

Tokens de portador para usuários no mesmo locatário são considerados válidos para o aplicativo de API protegido. Se você quiser garantir que apenas uma entidade de serviço possa chamar o aplicativo de API protegido, adicione o código no aplicativo de API protegido para verificar as seguintes declarações:

* `appid` deve ser igual à ID do cliente do aplicativo Azure AD que está associado ao chamador.
* `objectidentifier` deve ser a ID da entidade de serviço do chamador.

### Como proteger o aplicativo de API contra acesso do navegador

Se você não validar declarações no código no aplicativo de API protegido e se usar um aplicativo Azure AD diferente para o aplicativo de API protegido, verifique se a URL de Resposta do aplicativo Azure AD não é igual à URL base do aplicativo de API. Se a URL de Resposta apontar diretamente para o aplicativo de API protegido, um usuário no mesmo locatário do Azure AD poderá navegar até o aplicativo de API, fazer logon e chamar a API com êxito.

## <a id="tutorialstart"></a> Continuação dos tutoriais de introdução do .NET

Se você estiver seguindo a série de introdução do Node.js ou do Java para aplicativos de API, vá para a seção [Próximas etapas](#next-steps).

O restante deste artigo continua a série de introdução do .NET para aplicativos de API e pressupõe que você tenha concluído o [tutorial de autenticação de usuário](app-service-api-user-principal-authentication.md) e tenha o aplicativo de exemplo em execução no Azure com a autenticação de usuário habilitada.

## Configurar a autenticação no Azure

Nesta seção, você configurará o Serviço de Aplicativo para que as únicas solicitações HTTP que ele permita que cheguem ao aplicativo de API de camada de dados sejam aquelas que tiverem tokens de portador válidos do Azure AD.

Na seção a seguir, você configurará o aplicativo de API de camada intermediária para enviar as credenciais do aplicativo ao Azure AD, obter de volta um token de portador e enviar o token de portador ao aplicativo de API de camada de dados. Esse processo é ilustrado no diagrama.

![](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha de Aplicativo de API do aplicativo de API que você criou para ToDoListDataAPI (camada de dados) e clique em **Configurações**.

2. Na folha **Configurações**, localize a seção **Recursos** e clique em **Autenticação/Autorização**.

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, escolha **Fazer logon com o Active Directory do Azure**.

	Essa é a configuração que faz com que o Serviço de Aplicativo garanta que somente solicitações autenticadas cheguem ao aplicativo de API. Para solicitações com tokens de portador válido, o Serviço de Aplicativo passa os tokens para o aplicativo de API e popula os cabeçalhos HTTP com declarações comumente usadas para disponibilizar essas informações mais facilmente ao código.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**

	Com a opção **Expresso**, o Azure pode criar automaticamente um aplicativo do AAD no [locatário](https://msdn.microsoft.com/pt-BR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) do Azure AD.

	Você não precisa criar um locatário, pois cada conta do Azure tem um automaticamente.

7. Em **Modo de gerenciamento**, clique em **Criar novo aplicativo do AD**.

	O portal insere um valor padrão na caixa de entrada **Criar Aplicativo**.
	
	![](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	Por padrão, o aplicativo Azure AD tem o mesmo nome que o aplicativo de API. Se preferir, você pode inserir um nome diferente.

	Como alternativa, você pode usar um único aplicativo Azure AD para o aplicativo de API de chamada e o aplicativo de API protegido. Se escolher essa alternativa, você não precisará da opção **Criar novo aplicativo do AD** aqui, pois já criou um aplicativo Azure AD no início do tutorial de autenticação de usuário. Neste tutorial, você usará aplicativos do Azure AD separados para o aplicativo de API de chamada e o aplicativo de API protegido.

8. Anote o valor que está na caixa de entrada **Criar Aplicativo**; você procurará esse aplicativo do AAD no portal clássico do Azure posteriormente.

7. Clique em **OK**.

10. Na folha **Autenticação/Autorização**, clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	O Serviço de Aplicativo cria um aplicativo do Active Directory do Azure com a **URL de entrada** e a **URL de resposta** definidas automaticamente como a URL de seu aplicativo de API. O último valor habilita os usuários em seu locatário do AAD a fazer logon e acessar o aplicativo de API.

### Verificar se o aplicativo de API está protegido

1. Em um navegador, vá para a URL do aplicativo de API: na folha **Aplicativo de API** no portal do Azure, clique no link em **URL**. 

	Você será redirecionado para uma tela de logon, pois solicitações não autenticadas não têm permissão para chegar ao aplicativo de API.

	Se seu navegador for para a interface do usuário do Swagger, talvez já esteja conectado. Nesse caso, abra uma janela InPrivate ou Incognito e vá para a URL da interface do usuário do Swagger.

18. Faça logon com as credenciais de um usuário em seu locatário do AAD.

	Quando você estiver conectado, uma página "criado com êxito" será exibida no navegador.

## Configurar o projeto ToDoListAPI para adquirir e enviar o token do Azure AD

Nesta seção, você executará as seguintes tarefas:

* Adicionar código no aplicativo de API de camada intermediária que usa credenciais do aplicativo Azure AD para adquirir um token e enviá-lo com solicitações HTTP ao aplicativo de API de camada de dados.
* Obter as credenciais necessárias do Azure AD.
* Inserir as credenciais nas configurações de ambiente de tempo de execução do Serviço de Aplicativo do Azure no aplicativo de API de camada intermediária. 

### Configurar o projeto ToDoListAPI para adquirir e enviar o token do Azure AD

Faça as alterações a seguir no projeto ToDoListAPI no Visual Studio.

1. Remova as marcas de comentários de todo o código no arquivo *ServicePrincipal.cs*.

	Esse é o código que usa a ADAL para .NET para adquirir o token de portador do Azure AD. Ele usa vários valores de configuração que você definirá mais tarde no ambiente de tempo de execução do Azure. O código é o seguinte:

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**Observação:** esse código requer a ADAL para o pacote do NuGet do .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), que já está instalado no projeto. Se criasse o projeto do zero, você precisaria instalar este pacote. O pacote não é instalado automaticamente pelo modelo de novo projeto de aplicativo de API.

2. Em *Controladores/ToDoListController*, remova as marcas de comentário do código no método `NewDataAPIClient` que adiciona o token a solicitações HTTP no cabeçalho de autorização.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Implantar o projeto ToDoListAPI. (Clique com o botão direito do mouse no projeto e clique em **Publicar > Publicar**.)

4. Feche a janela do navegador que será aberta depois que o projeto for implantado com êxito.

### Obter valores de configuração do Azure AD

11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

12. Na guia **Diretório**, clique no locatário do AAD.

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API ToDoListDataAPI (camada de dados).

16. Clique na guia **Configurar**.

5. Copie o valor de **ID do cliente** e salve-o em algum local em que você possa obtê-lo posteriormente.

8. No portal clássico do Azure, volte à lista de **Aplicativos que minha empresa possui** e clique no aplicativo do AAD que você criou para o aplicativo de API ToDoListAPI (camada intermediária).

16. Clique na guia **Configurar**.

5. Copie o valor de **ID do cliente** e salve-o em algum local em que você possa obtê-lo posteriormente.

6. Em **Chaves**, selecione **1 ano** na lista suspensa **Selecionar duração**.

6. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie o valor de chave e salve-o em algum local em que você possa obtê-lo posteriormente.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### Definir configurações do Azure AD no ambiente de tempo de execução do aplicativo de API de camada intermediária

1. Vá para o [portal do Azure](https://portal.azure.com/) e navegue até a folha **Aplicativo de API** do aplicativo de API que hospeda o projeto TodoListAPI (camada intermediária).

2. Clique em **Configurações > Configurações do aplicativo**.

3. Na seção **Configurações do aplicativo**, adicione as seguintes chaves e valores:

	|Chave|Valor|Exemplo
	|---|---|---|
	|ida:Authority|https://login.microsoftonline.com/{your Nome do locatário do Azure AD}|https://login.microsoftonline.com/contoso.onmicrosoft.com|
	|ida:ClientId|ID do cliente do aplicativo de chamada (ToDoListAPI)|960adec2-b74a-484a-960adec2-b74a-484a|
	|ida:ClientSecret|Chave de aplicativo do aplicativo de chamada (ToDoListAPI)|oCgdj3EYLfnR0p6iR3UvHFAfkn+zQB+0VqZT/6=
	|ida:Resource|ID do cliente do aplicativo chamado (ToDoListDataAPI)|e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8|

	Se estivesse usando um único aplicativo Azure AD para o aplicativo de chamada de API e o aplicativo de API protegido, você usaria o mesmo valor em `ida:ClientId` e `ida:Resource`.

	O código usa o ConfigurationManager para obter esses valores, para que eles possam ser armazenados no arquivo Web.config do projeto ou no ambiente de tempo de execução do Azure. Enquanto um aplicativo do ASP.NET é executado no Serviço de Aplicativo do Azure, as configurações de ambiente automaticamente substituem as configurações de Web.config. As configurações de ambiente geralmente são uma [maneira mais segura de armazenar informações confidenciais em comparação com um arquivo Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### Testar o aplicativo

1. Em um navegador, vá para a URL HTTPS do aplicativo Web de front-end do AngularJS.

2. Clique na guia **Lista de tarefas pendentes** e faça logon usando as credenciais de um usuário de seu locatário do Azure AD.

4. Adicione itens pendentes para verificar se o aplicativo está funcionando.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	Se o aplicativo não funcionar conforme o esperado, verifique todas as configurações que você inseriu no portal do Azure. Se todas as configurações parecerem corretas, confira a seção [Solução de problemas](#troubleshooting) mais adiante neste tutorial.

## Proteger o aplicativo de API contra acesso do navegador

Para este tutorial, você criou um aplicativo Azure AD separado para o aplicativo de API ToDoListDataAPI (camada de dados). Como você viu, quando o Serviço de Aplicativo cria um aplicativo do AAD, ele configura o aplicativo do AAD de uma maneira que habilita um usuário a ir para a URL do aplicativo de API em um navegador e fazer logon. Isso significa que é possível que um usuário final em seu locatário do Azure AD (e não apenas uma entidade de serviço) acesse a API.

Se quiser impedir o acesso do navegador sem escrever código no aplicativo de API protegido, você poderá alterar a **URL de resposta** no aplicativo do AAD para que ela seja diferente da URL de base do aplicativo de API.

### Desabilitar o acesso do navegador

1. Na guia **Configurar** do portal clássico do aplicativo do AAD criado para o projeto TodoListService, altere o valor do campo **URL de Resposta** para que seja uma URL válida, mas não a URL do aplicativo de API.
 
2. Clique em **Salvar**.

### Verificar se o acesso de navegador não funciona mais

Anteriormente, você verificou que pode ir para a URL do aplicativo de API por meio de um navegador fazendo logon com as credenciais de um usuário individual. Nesta seção, você verificará se que isso não é mais possível.

1. Em uma nova janela do navegador, vá para a URL do aplicativo de API novamente.

2. Faça logon quando for solicitado a fazer isso.

3. O logon é bem-sucedido, mas leva a uma página de erro.

	Você configurou o aplicativo do AAD para que os usuários no locatário do AAD não possam fazer logon e acessar a API por meio de um navegador. Você ainda pode acessar o aplicativo de API usando um token de entidade de serviço, que você pode verificar indo para a URL do aplicativo Web e adicionando mais itens pendentes.

## Restringir o acesso a uma entidade de serviço específica  

Agora, qualquer chamador que possa obter um token para um usuário ou uma entidade de serviço em seu locatário do Azure AD poderá chamar o aplicativo de API TodoListDataAPI (camada de dados). Convém garantir que o aplicativo de API de camada de dados só aceite chamadas do aplicativo de API TodoListAPI (camada intermediária) e apenas de uma entidade de serviço específica.

Você pode adicionar essas restrições adicionando código para validar as declarações `appid` e `objectidentifier` em chamadas de entrada.

Para este tutorial, você coloca o código que valida a ID do aplicativo e a ID da entidade de serviço diretamente em suas ações do controlador. As alternativas são usar um atributo `Authorize` personalizado ou fazer essa validação nas sequências de inicialização (por exemplo, middleware OWIN).

Faça as alterações a seguir no projeto TodoListDataAPI.

2. Abra o arquivo *Controllers/TodoListController.cs*.

3. Remova as marcas de comentários das linhas que definem `trustedCallerClientId` e `trustedCallerServicePrincipalId`.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Remova as marcas de comentários do código no método CheckCallerId. Esse método é chamado no início de cada método de ação no controlador.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Reimplante o projeto ToDoListDataAPI no Serviço de Aplicativo do Azure.

6. No navegador, vá para a URL HTTPS do aplicativo Web de front-end do AngularJS e, na home page, clique na guia **Lista de tarefas pendentes**.

	O aplicativo não funciona porque as chamadas ao back-end estão falhando. O novo código está verificando o appid e o objectidentifier reais, mas ainda não tem os valores corretos em relação aos quais deve verificá-los. O Console de Ferramentas de Desenvolvedor do navegador relata que o servidor está retornando um erro HTTP 401.

	![](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	Nas etapas a seguir, você configura os valores esperados.

8. Usando o PowerShell do Azure AD, obtenha o valor da entidade de serviço do aplicativo do Azure AD que você criou para o projeto TodoListWebApp.

	a. Para obter instruções sobre como instalar o Azure PowerShell e conectar-se à sua assinatura, confira [Uso do PowerShell do Azure com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).

	b. Para obter uma lista de entidades de serviço, execute o comando `Login-AzureRmAccount` e, em seguida, o comando `Get-AzureRmADServicePrincipal`.

	c. Localize o objectid da entidade de serviço do aplicativo TodoListAPI e salve-o em um local do qual você possa copiá-lo mais tarde.

7. No portal do Azure, navegue até a folha do aplicativo Web para o qual você implantou o projeto ToDoListAngular.

9. Clique em **Configurações > Configurações do aplicativo**.

3. Na seção **Configurações do aplicativo**, adicione as seguintes chaves e valores:

	|Chave|Valor|Exemplo
	|---|---|---|
	|todo:TrustedCallerServicePrincipalId|Id da entidade de serviço do aplicativo de chamada|4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072|
	|todo:TrustedCallerClientId|ID do cliente do aplicativo de chamada ‒ copiada do aplicativo do AAD TodoListAPI|960adec2-b74a-484a-960adec2-b74a-484a|

6. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. No navegador, retorne à URL do aplicativo Web e, na home page, clique na guia **Lista**.

	Desta vez, o aplicativo funciona como esperado, pois a ID do aplicativo chamador confiável e a ID da entidade de serviço são os valores esperados.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## Como criar os projetos do zero

Os dois projetos de API Web foram criados usando o modelo de projeto **Aplicativo de API do Azure** e substituindo o controlador de Valores padrão por um controlador ToDoList. Para adquirir tokens de entidade de serviço do Azure AD no projeto ToDoListAPI, foi instalado o pacote NuGet da [ADAL (Biblioteca de Autenticação do Active Directory) para .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)
 
Para obter informações sobre como criar um aplicativo de página única do AngularJS com um back-end de API Web como ToDoListAngular, confira [Laboratório prático: criar um SPA (aplicativo de página única) com a API Web do ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obter informações sobre como adicionar código de autenticação do Azure AD, confira [Como proteger aplicativos de página única do AngularJS com o Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## Solucionar problemas

Se você executar com êxito o aplicativo sem autenticação e, depois, ele não funcionar quando você implementar a autenticação, na maioria das vezes o problema será decorrente de configurações incorretas ou inconsistentes. Comece verificando todas as configurações no Serviço de Aplicativo do Azure e no Active Directory do Azure. Aqui estão algumas sugestões específicas:

* Depois de configurar o código em um projeto, verifique se você reimplantou esse projeto e não um dos outros.
* Para as configurações definidas na folha **Configurações do Aplicativo** do portal do Azure, verifique se o aplicativo Web ou o aplicativo de API correto foi selecionado quando as configurações foram inseridas.
* Verifique se você está usando URLs HTTPS no navegador, não URLs HTTP.
* Verifique se CORS ainda está habilitado no aplicativo de API de camada intermediária, permitindo chamadas à camada intermediária por meio da URL HTTPS do front-end. Caso esteja em dúvida se o problema está relacionado ao CORS, tente usar "*" como a URL de origem permitida. **Importante:** algumas mensagens de erro do Console de Ferramentas de Desenvolvedor do navegador podem relatar um erro de CORS quando o problema real é a autenticação da camada de dados. Você pode verificar se esse é o caso desabilitando temporariamente a autenticação para o aplicativo de API ToDoListDataAPI.
* Obtenha o máximo possível de informações nas mensagens de erro definindo o [modo customErrors como Desativado](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Se outros métodos falharem, tente uma [sessão de depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) e examine os valores das variáveis que são passadas para o código que obtém o token de portador em ToDoListAPI e o código que valida valores do Azure AD recebidos em ToDoListDataAPI. Observe que o código pode selecionar valores de configuração de várias fontes diferentes. Portanto, é possível que ocorram surpresas dessa maneira. Por exemplo, se você nomear incorretamente `ida:ClientId` como `ida:ClientID` ao definir as configurações do Serviço de Aplicativo, o código poderá receber o valor `ida:ClientId` que está procurando do arquivo Web.config, ignorando a configuração do Serviço de Aplicativo. 

## Próximas etapas

Este é o último artigo da série de introdução aos Aplicativos de API.

Para saber mais sobre o Active Directory do Azure, confira os recursos a seguir.

* [Guia dos desenvolvedores do AD do Azure](http://aka.ms/aaddev)
* [Cenários do AD do Azure](http://aka.ms/aadscenarios)
* [Exemplos do AD do Azure](http://aka.ms/aadsamples)

Para saber mais sobre outras maneiras de implantar projetos do Visual Studio em aplicativos de API, usando o Visual Studio ou [automatizando a implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle do código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), confira [Como implantar um aplicativo do Serviço de Aplicativo do Azure](web-sites-deploy.md).

<!---HONumber=AcomDC_0204_2016-->