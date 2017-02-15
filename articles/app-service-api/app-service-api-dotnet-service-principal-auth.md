---
title: "Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure em cenários serviço a serviço."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: 7ca0bab2-1d29-4d51-b779-dce0edd34f8b
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 234ab46713657c7400148210c6029c63afd35e96


---
# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure
## <a name="overview"></a>Visão geral
Este artigo explica como usar a autenticação do Serviço de Aplicativo para acesso *interno* aos aplicativos de API. Um cenário interno é quando você tem um aplicativo de API que deseja que seja consumível apenas por seu próprio código de aplicativo. A maneira recomendada de implementar esse cenário no Serviço de Aplicativo é usar o Azure AD para proteger o aplicativo de API chamado. Você chama o aplicativo de API protegido com um token de portador que obtém do Azure AD fornecendo credenciais (entidade de serviço) de identidade do aplicativo. Para ver alternativas ao uso do Azure AD, consulte a seção **Autenticação serviço a serviço** da [Visão geral de autenticação do Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

Neste artigo, você aprenderá o seguinte:

* Como usar o Active Directory do Azure (Azure AD) para proteger um aplicativo de API contra acesso não autenticado.
* Como consumir um aplicativo de API protegido de um aplicativo de API, um aplicativo Web ou um aplicativo móvel usando credenciais de entidade de serviço (identidade de aplicativo) do Azure AD. Para obter informações sobre como consumir de um aplicativo lógico, consulte [Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Como verificar se o aplicativo de API protegido não pode ser chamado de um navegador por usuários conectados.
* Como verificar se o aplicativo de API protegido só pode ser chamado por uma entidade de serviço do Azure AD específica.

O artigo contém duas seções:

* A seção [Como configurar a autenticação de entidade de serviço no Serviço de Aplicativo do Azure](#authconfig) explica em termos gerais como configurar a autenticação para qualquer aplicativo de API e como consumir o aplicativo de API protegido. Esta seção aplica-se igualmente a todas as estruturas às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java.
* A partir da seção [Continuação dos tutoriais de introdução ao .NET](#tutorialstart) , o tutorial o guiará você pela configuração de um cenário de "acesso interno" para um aplicativo .NET de exemplo em execução no Serviço de Aplicativo. 

## <a name="a-idauthconfiga-how-to-configure-service-principal-authentication-in-azure-app-service"></a><a id="authconfig"></a> Como configurar a autenticação da entidade de serviço no Serviço de Aplicativo do Azure
Esta seção fornece instruções gerais que se aplicam a qualquer aplicativo de API. Para obter etapas específicas para o aplicativo de exemplo .NET de Lista de Tarefas Pendentes, vá para [Continuação da série de tutoriais de introdução aos Aplicativos de API .NET](#tutorialstart).

1. No [Portal do Azure](https://portal.azure.com/), navegue até a folha **Configurações** do aplicativo de API que você deseja proteger, localize a seção **Recursos** e clique em **Autenticação/Autorização**.
   
    ![Autenticação/Autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)
2. Na folha **Autenticação/Autorização**, clique em **Ativada**.
3. Na lista suspensa **Ação a tomar quando a solicitação não está autenticada**, selecione **Efetuar logon com o Azure Active Directory**.
4. Em **Provedores de Autenticação**, selecione **Azure Active Directory**.
   
    ![Folha Autenticação/Autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
5. Configure a folha **Configurações do Azure Active Directory** para criar um novo aplicativo Azure AD ou use um aplicativo Azure AD existente se já tiver um que deseja usar.
   
    Cenários internos normalmente envolvem um aplicativo de API que chama um aplicativo de API. Você pode usar aplicativos do Azure AD separados para cada aplicativo de API ou apenas um aplicativo Azure AD.
   
    Para obter instruções detalhadas sobre essa folha, consulte [Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).
6. Quando você terminar de usar a folha de configuração do provedor de autenticação, clique em **OK**.
7. Na folha **Autenticação/Autorização**, clique em **Salvar**.
   
    ![Clique em Salvar](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Quando isso for feito, o Serviço de Aplicativo permitirá somente solicitações de chamadores no locatário do Azure AD configurado. Nenhum código de autenticação ou de autorização é necessário no aplicativo de API protegido. O token de portador é passado para o aplicativo de API, juntamente com declarações usadas com frequência em cabeçalhos HTTP, e você pode ler essas informações no código para validar que as solicitações são de um chamador específico, como uma entidade de serviço.

Essa funcionalidade de autenticação funciona da mesma maneira para todas as linguagens às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java. 

#### <a name="how-to-consume-the-protected-api-app"></a>Como consumir o aplicativo de API protegido
O chamador deve fornecer um token de portador do Azure AD com chamadas de API. Para obter um token de portador usando credenciais de entidade de serviço, o chamador usa a ADAL (Biblioteca de Autenticação do Active Directory para [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) ou [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Para obter um token, o código que chama a ADAL fornece a ela as seguintes informações:

* O nome de seu locatário do Azure AD.
* A ID e o segredo do cliente (chave do aplicativo) do aplicativo Azure AD associado ao chamador.
* A ID do cliente do aplicativo Azure AD associado ao aplicativo de API protegido. (Se apenas um aplicativo Azure AD for usado, essa será a mesma ID de cliente usada para o chamador.)

Esses valores estão disponíveis nas páginas do Azure AD do [portal clássico do Azure](https://manage.windowsazure.com/).

Depois que o token for adquirido, o chamador o incluirá em solicitações HTTP no cabeçalho de Autorização.  O Serviço de Aplicativo valida o token e permite que as solicitações cheguem ao aplicativo de API protegido.

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>Como proteger o aplicativo de API contra acesso por usuários no mesmo locatário
Tokens de portador para usuários no mesmo locatário são considerados válidos para o aplicativo de API protegido.  Se você quiser garantir que apenas uma entidade de serviço possa chamar o aplicativo de API protegido, adicione o código no aplicativo de API protegido para validar as seguintes declarações do token:

* `appid` deve ser a ID do cliente do aplicativo Azure AD que está associada ao chamador. 
* `oid` (`objectidentifier`) deve ser a ID da entidade de serviço do chamador. 

O Serviço de Aplicativo também fornece a declaração `objectidentifier` no cabeçalho X-MS-CLIENT-PRINCIPAL-ID.

### <a name="how-to-protect-the-api-app-from-browser-access"></a>Como proteger o aplicativo de API contra acesso do navegador
Se você não validar declarações no código no aplicativo de API protegido e se usar um aplicativo Azure AD diferente para o aplicativo de API protegido, verifique se a URL de Resposta do aplicativo Azure AD não é igual à URL base do aplicativo de API. Se a URL de Resposta apontar diretamente para o aplicativo de API protegido, um usuário no mesmo locatário do Azure AD poderá navegar até o aplicativo de API, fazer logon e chamar a API com êxito.

## <a name="a-idtutorialstarta-continuing-the-net-api-apps-tutorial-series"></a><a id="tutorialstart"></a> Continuar a série de tutoriais de Aplicativos de API do .NET
Se você estiver seguindo a série de tutoriais do Node.js ou do Java para aplicativos de API, vá para a seção [Próximas etapas](#next-steps) . 

O restante deste artigo continua a série de tutoriais de Aplicativos de API do .NET e pressupõe que você tenha concluído o [tutorial de autenticação de usuário](app-service-api-dotnet-user-principal-auth.md) e possua o aplicativo de exemplo em execução no Azure com a autenticação de usuário habilitada.

## <a name="set-up-authentication-in-azure"></a>Configurar a autenticação no Azure
Nesta seção, você configurará o Serviço de Aplicativo para que as únicas solicitações HTTP que ele permita que cheguem ao aplicativo de API de camada de dados sejam aquelas que tiverem tokens de portador válidos do Azure AD. 

Na seção a seguir, você configurará o aplicativo de API de camada intermediária para enviar as credenciais do aplicativo ao Azure AD, obter de volta um token de portador e enviar o token de portador ao aplicativo de API de camada de dados. Esse processo é ilustrado no diagrama.

![Diagrama do serviço de autenticação](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Se você enfrenta problemas ao seguir as instruções do tutorial, consulte a seção [Solução de problemas](#troubleshooting) no final do tutorial. 

1. No [Portal do Azure](https://portal.azure.com/), navegue até a folha **Configuração** do aplicativo de API que você criou para ToDoListDataAPI (camada de dados) e clique em **Configurações**.
2. Na folha **Configurações**, localize a seção **Recursos** e clique em **Autenticação / Autorização**.
   
    ![Autenticação/Autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)
3. Na folha **Autenticação/Autorização**, clique em **Ativada**.
4. Na lista suspensa **Ação a tomar quando a solicitação não está autenticada**, selecione **Efetuar logon com o Azure Active Directory**.
   
    Essa é a configuração que faz com que o Serviço de Aplicativo garanta que somente solicitações autenticadas cheguem ao aplicativo de API. Para solicitações com tokens de portador válido, o Serviço de Aplicativo passa os tokens para o aplicativo de API e popula os cabeçalhos HTTP com declarações comumente usadas para disponibilizar essas informações mais facilmente ao código.
5. Em **Provedores de Autenticação**, clique em **Azure Active Directory**.
   
    ![Folha Autenticação/Autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)
6. Na folha **Configurações do Azure Active Directory**, clique em **Expresso**.
   
    Com a opção **Expresso**, o Azure pode criar automaticamente um aplicativo do AAD no [locatário](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) do Azure AD. 
   
    Você não precisa criar um locatário, pois cada conta do Azure tem um automaticamente.
7. Em **Modo de gerenciamento**, clique em **Criar novo aplicativo do AD** se ainda não estiver selecionado.
   
    O portal insere um valor padrão na caixa de entrada **Criar Aplicativo** . Por padrão, o aplicativo Azure AD tem o mesmo nome que o aplicativo de API. Se preferir, você pode inserir um nome diferente.
   
    ![Configurações do Azure AD](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)
   
    **Observação**: como alternativa, você pode usar um único aplicativo Azure AD para o aplicativo de API de chamada e o aplicativo de API protegido. Se escolher essa alternativa, você não precisará da opção **Criar novo aplicativo do AD** aqui, pois já criou um aplicativo Azure AD no início do tutorial de autenticação de usuário. Neste tutorial, você usará aplicativos do Azure AD separados para o aplicativo de API de chamada e o aplicativo de API protegido.
8. Anote o valor que está na caixa de entrada **Criar Aplicativo** ; você procurará esse aplicativo do AAD no portal clássico do Azure posteriormente.
9. Clique em **OK**.
10. Na folha **Autenticação/Autorização**, clique em **Salvar**.
    
    ![Clique em Salvar](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)
    
    O Serviço de Aplicativo cria um aplicativo do Azure Active Directory com a **URL de entrada** e a **URL de resposta** definidas automaticamente como a URL de seu aplicativo de API. O último valor habilita os usuários em seu locatário do AAD a fazer logon e acessar o aplicativo de API.

### <a name="verify-that-the-api-app-is-protected"></a>Verificar se o aplicativo de API está protegido
1. Em um navegador, vá para a URL do aplicativo de API: na folha **Aplicativo de API** no portal do Azure, clique no link na **URL**. 
   
    Você será redirecionado para uma tela de logon, pois solicitações não autenticadas não têm permissão para chegar ao aplicativo de API. 
   
    Se seu navegador for para a interface do usuário do Swagger, talvez já esteja conectado. Nesse caso, abra uma janela InPrivate ou Incognito e vá para a URL da interface do usuário do Swagger.
2. Faça logon com as credenciais de um usuário em seu locatário do AAD.
   
   Quando você estiver conectado, uma página "criado com êxito" será exibida no navegador.

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar o projeto ToDoListAPI para adquirir e enviar o token do Azure AD
Nesta seção, você executará as seguintes tarefas:

* Adicionar código no aplicativo de API de camada intermediária que usa credenciais do aplicativo Azure AD para adquirir um token e enviá-lo com solicitações HTTP ao aplicativo de API de camada de dados.
* Obter as credenciais necessárias do Azure AD.
* Inserir as credenciais nas configurações de ambiente de tempo de execução do Serviço de Aplicativo do Azure no aplicativo de API de camada intermediária. 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar o projeto ToDoListAPI para adquirir e enviar o token do Azure AD
Faça as alterações a seguir no projeto ToDoListAPI no Visual Studio.

1. Remova as marcas de comentários de todo o código no arquivo *ServicePrincipal.cs* .
   
    Esse é o código que usa a ADAL para .NET para adquirir o token de portador do Azure AD.  Ele usa vários valores de configuração que você definirá mais tarde no ambiente de tempo de execução do Azure. O código é o seguinte: 
   
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
2. Em *Controllers/ToDoListController*, remova as marcas de comentário do código no método `NewDataAPIClient` que adiciona o token a solicitações HTTP no cabeçalho de autorização.
   
        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
3. Implantar o projeto ToDoListAPI. (Clique com o botão direito do mouse no projeto e em **Publicar > Publicar**.)
   
    O Visual Studio implanta o projeto e abre a URL base do aplicativo Web em um navegador. Isso mostrará uma página de erro 403, que é normal em uma tentativa de ir para uma URL base da API Web em um navegador.
4. Feche o navegador.

### <a name="get-azure-ad-configuration-values"></a>Obter valores de configuração do Azure AD
1. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.
2. Na guia **Diretório** , clique no locatário do AAD.
3. Clique em **Aplicativos > Aplicativos que minha empresa tem** e clique na marca de seleção.
4. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API ToDoListDataAPI (camada de dados).
5. Clique na guia **Configurar** .
6. Copie o valor de **ID do cliente** e salve-o em algum local em que você possa obtê-lo posteriormente. 
7. No Portal Clássico do Azure, volte à lista de **Aplicativos que minha empresa possui**e clique no aplicativo do AAD que você criou para o aplicativo de API de camada intermediária ToDoListAPI (aquele que você criou no tutorial anterior, não o criado neste tutorial).
8. Clique na guia **Configurar** .
9. Copie o valor de **ID do cliente** e salve-o em algum local em que você possa obtê-lo posteriormente.
10. Em **Chaves**, selecione **1 ano** na lista suspensa **Selecionar duração**.
11. Clique em **Salvar**.
    
     ![Gerar chave do aplicativo](./media/app-service-api-dotnet-service-principal-auth/genkey.png)
12. Copie o valor de chave e salve-o em algum local em que você possa obtê-lo posteriormente.
    
     ![Copiar a nova chave do aplicativo](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>Definir configurações do Azure AD no ambiente de tempo de execução do aplicativo de API de camada intermediária
1. Vá para o [Portal do Azure](https://portal.azure.com/)e navegue até a folha **Aplicativo de API** do aplicativo de API que hospeda o projeto TodoListAPI (camada intermediária).
2. Clique em **Configurações > Configurações do Aplicativo**.
3. Na seção **Configurações do aplicativo** , adicione as seguintes chaves e valores:
   
   | **Chave** | ida:Authority |
   | --- | --- |
   | **Valor** |https://login.microsoftonline.com/{your Azure AD tenant name} |
   | **Exemplo** |https://login.microsoftonline.com/contoso.onmicrosoft.com |
   
   | **Chave** | ida:ClientId |
   | --- | --- |
   | **Valor** |ID do cliente do aplicativo de chamada (camada intermediária - ToDoListAPI) |
   | **Exemplo** |960adec2-b74a-484a-960adec2-b74a-484a |
   
   | **Chave** | ida:ClientSecret |
   | --- | --- |
   | **Valor** |Chave de aplicativo do aplicativo de chamada (camada intermediária - ToDoListAPI) |
   | **Exemplo** |e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |
   
   | **Chave** | ida:Resource |
   | --- | --- |
   | **Valor** |ID do cliente do aplicativo chamado (camada de dados - ToDoListDataAPI) |
   | **Exemplo** |e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |
   
    **Observação**: para `ida:Resource`, use a **ID do cliente** do aplicativo e não o **URI da ID do aplicativo**.
   
    `ida:ClientId` e `ida:Resource` são diferentes valores para este tutorial porque você está usando aplicativos Azure AD separados para a camada intermediária e para a camada de dados. Se estivesse usando um único aplicativo Azure AD para o aplicativo de chamada de API e para o aplicativo de API protegido, você usaria o mesmo valor em `ida:ClientId` e `ida:Resource`.
   
    O código usa o ConfigurationManager para obter esses valores, para que eles possam ser armazenados no arquivo Web.config do projeto ou no ambiente de tempo de execução do Azure. Enquanto um aplicativo do ASP.NET é executado no Serviço de Aplicativo do Azure, as configurações de ambiente automaticamente substituem as configurações de Web.config. As configurações de ambiente geralmente são uma [maneira mais segura de armazenar informações confidenciais em comparação com um arquivo Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).
4. Clique em **Salvar**.
   
    ![Clique em Salvar](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>Testar o aplicativo
1. Em um navegador, vá para a URL HTTPS do aplicativo Web de front-end do AngularJS.
2. Clique na guia **Lista de Tarefas Pendentes** e faça logon usando as credenciais de um usuário de seu locatário do Azure AD. 
3. Adicione itens pendentes para verificar se o aplicativo está funcionando.
   
    ![A página Lista de tarefas pendentes](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)
   
    Se o aplicativo não funcionar conforme o esperado, verifique todas as configurações que você inseriu no portal do Azure. Se todas as configurações parecerem corretas, confira a seção [Solução de problemas](#troubleshooting) mais adiante neste tutorial.

## <a name="protect-the-api-app-from-browser-access"></a>Proteger o aplicativo de API contra acesso do navegador
Para este tutorial, você criou um aplicativo Azure AD separado para o aplicativo de API ToDoListDataAPI (camada de dados). Como você viu, quando o Serviço de Aplicativo cria um aplicativo do AAD, ele configura o aplicativo do AAD de uma maneira que habilita um usuário a ir para a URL do aplicativo de API em um navegador e fazer logon. Isso significa que é possível que um usuário final em seu locatário do Azure AD (e não apenas uma entidade de serviço) acesse a API. 

Se quiser impedir o acesso do navegador sem escrever código no aplicativo de API protegido, você poderá alterar a **URL de resposta** no aplicativo do AAD para que ela seja diferente da URL de base do aplicativo de API. 

### <a name="disable-browser-access"></a>Desabilitar o acesso do navegador
1. Na guia **Configurar** do portal clássico do aplicativo do AAD criado para o projeto TodoListService, altere o valor do campo **URL de Resposta** para que seja uma URL válida, mas não a URL do aplicativo de API.
2. Clique em **Salvar**.

### <a name="verify-browser-access-no-longer-works"></a>Verificar se o acesso de navegador não funciona mais
Anteriormente, você verificou que pode ir para a URL do aplicativo de API por meio de um navegador fazendo logon com as credenciais de um usuário individual. Nesta seção, você verá que isso não é mais possível. 

1. Em uma nova janela do navegador, vá para a URL do aplicativo de API novamente.
2. Faça logon quando for solicitado a fazer isso.
3. O logon é bem-sucedido, mas leva a uma página de erro.
   
    Você configurou o aplicativo do AAD para que os usuários no locatário do AAD não possam fazer logon e acessar a API por meio de um navegador. Você ainda pode acessar o aplicativo de API usando um token de entidade de serviço, que você pode verificar indo para a URL do aplicativo Web e adicionando mais itens pendentes.

## <a name="restrict-access-to-a-particular-service-principal"></a>Restringir o acesso a uma entidade de serviço específica
Agora, qualquer chamador que possa obter um token para um usuário ou uma entidade de serviço em seu locatário do Azure AD poderá chamar o aplicativo de API TodoListDataAPI (camada de dados). Convém garantir que o aplicativo de API de camada de dados só aceite chamadas do aplicativo de API TodoListAPI (camada intermediária) e apenas de uma entidade de serviço específica. 

Você pode adicionar essas restrições adicionando código para validar as declarações `appid` e `objectidentifier` em chamadas de entrada.

Para este tutorial, você coloca o código que valida a ID do aplicativo e a ID da entidade de serviço diretamente em suas ações do controlador.  As alternativas são usar um atributo `Authorize` personalizado ou fazer essa validação nas sequências de inicialização (por exemplo, middleware OWIN). Para obter um exemplo desse último item, consulte [este aplicativo de exemplo](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs). 

Faça as alterações a seguir no projeto TodoListDataAPI.

1. Abra o arquivo *Controllers/TodoListController.cs* .
2. Remova as marcas de comentários das linhas que definem `trustedCallerClientId` e `trustedCallerServicePrincipalId`.
   
        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];
3. Remova as marcas de comentários do código no método CheckCallerId. Esse método é chamado no início de cada método de ação no controlador. 
   
        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }
4. Reimplante o projeto ToDoListDataAPI no Serviço de Aplicativo do Azure.
5. No navegador, vá para a URL HTTPS do aplicativo Web de front-end do AngularJS e, na home page, clique na guia **Lista de Tarefas Pendentes** .
   
    O aplicativo não funciona porque as chamadas ao back-end estão falhando. O novo código está verificando o appid e o objectidentifier reais, mas ainda não tem os valores corretos em relação aos quais deve verificá-los. O Console de Ferramentas de Desenvolvedor do navegador relata que o servidor está retornando um erro HTTP 401.
   
    ![Erro no Console de Ferramentas de Desenvolvedor](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)
   
    Nas etapas a seguir, você configura os valores esperados.
6. Usando o PowerShell do Azure AD, obtenha o valor da entidade de serviço do aplicativo do Azure AD que você criou para o projeto TodoListWebApp.
   
    a. Para obter instruções sobre como instalar o Azure PowerShell e conectar-se à sua assinatura, confira [Usar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).
   
    b. Para obter uma lista de entidades de serviço, execute o comando `Login-AzureRmAccount` e, em seguida, o comando `Get-AzureRmADServicePrincipal`.
   
    c. Localize o objectid da entidade de serviço do aplicativo TodoListAPI e salve-o em um local do qual você possa copiá-lo mais tarde.
7. No portal do Azure, navegue até a folha do aplicativo de API no qual o projeto ToDoListDataAPI foi implantado.
8. Clique em **Configurações > Configurações do Aplicativo**.
9. Na seção **Configurações do aplicativo** , adicione as seguintes chaves e valores:
   
   | **Chave** | todo:TrustedCallerServicePrincipalId |
   | --- | --- |
   | **Valor** |Id da entidade de serviço do aplicativo de chamada |
   | **Exemplo** |4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |
   
   | **Chave** | todo:TrustedCallerClientId |
   | --- | --- |
   | **Valor** |ID do cliente do aplicativo de chamada ‒ copiada do aplicativo Azure AD TodoListAPI |
   | **Exemplo** |960adec2-b74a-484a-960adec2-b74a-484a |
10. Clique em **Salvar**.
    
     ![Clique em Salvar](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)
11. No navegador, retorne à URL do aplicativo Web e, na home page, clique na guia **Lista de Tarefas Pendentes** .
    
     Desta vez, o aplicativo funciona como esperado, pois a ID do aplicativo chamador confiável e a ID da entidade de serviço são os valores esperados.
    
     ![A página Lista de tarefas pendentes](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>Como criar os projetos do zero
Os dois projetos de API Web foram criados usando o modelo de projeto **Aplicativo de API do Azure** e substituindo o controlador de Valores padrão por um controlador ToDoList. Para adquirir tokens de entidade de serviço do Azure AD no projeto ToDoListAPI, foi instalado o pacote NuGet da [ADAL (Biblioteca de Autenticação do Active Directory) para .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Para obter informações sobre como criar um aplicativo de página única do AngularJS com um back-end de API Web como ToDoListAngular, confira [Laboratório prático: criar um SPA (aplicativo de página única) com a API Web ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obter informações sobre como adicionar código de autenticação do Azure AD, confira [Como proteger aplicativos de página única do AngularJS com o Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## <a name="troubleshooting"></a>Solução de problemas
[!INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Não confunda ToDoListAPI (camada intermediária) e ToDoListDataAPI (camada de dados). Por exemplo, neste tutorial, você pode adicionar autenticação ao aplicativo de API de camada de dados, **mas a chave do aplicativo deve vir do aplicativo Azure AD que você criou para o aplicativo de API de camada intermediária**.

## <a name="next-steps"></a>Próximas etapas
Este é o último tutorial da série de Aplicativos de API. 

Para saber mais sobre o Active Directory do Azure, confira os recursos a seguir.

* [Guia dos desenvolvedores do AD do Azure](http://aka.ms/aaddev)
* [Cenários do AD do Azure](http://aka.ms/aadscenarios)
* [Exemplos do AD do Azure](http://aka.ms/aadsamples)
  
    [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) é semelhante ao que é mostrado neste tutorial, mas sem usar a autenticação do Serviço de Aplicativo.

Para saber mais sobre outras maneiras de implantar projetos do Visual Studio em aplicativos de API, usando o Visual Studio ou [automatizando a implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle do código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), confira [Como implantar um aplicativo do Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md).




<!--HONumber=Nov16_HO3-->


