---
title: "Criar um aplicativo de linha de negócios do Azure com autenticação do AD FS | Microsoft Docs"
description: "Saiba como criar um aplicativo de linha de negócios no Serviço de Aplicativo do Azure que realiza a autenticação com o STS local. Este tutorial destina-se ao AD FS como STS local."
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 0fa9f7a1-37bd-4d11-845f-aeff6fc9e4ca
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1895094b28d9596eec644078b6f9a877b526b89e
ms.contentlocale: pt-br
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-line-of-business-azure-app-with-ad-fs-authentication"></a>Criar um aplicativo de linha de negócios do Azure com autenticação do AD FS
Este artigo mostra como criar um aplicativo de linha de negócios ASP.NET MVC no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) usando os [Serviços de Federação do Active Directory](http://technet.microsoft.com/library/hh831502.aspx) locais como o provedor de identidade. Este cenário poderá funcionar quando você desejar criar aplicativos de linha de negócios no Serviço de Aplicativo do Azure, mas sua organização exigir que os dados de diretório sejam armazenados no local.

> [!NOTE]
> Para uma visão geral das diferentes opções de autenticação e autorização corporativas para o Serviço de Aplicativo do Azure, consulte [Autenticar com o Active Directory local em seu aplicativo do Azure](web-sites-authentication-authorization.md).
> 
> 

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>O que você compilará
Você criará um aplicativo básico do ASP.NET nos Aplicativos Web do Serviço de Aplicativo do Azure com os seguintes recursos:

* Autentica usuários no AD FS
* Usa `[Authorize]` para autorizar usuários para diferentes ações CRUD
* Configuração estática para depuração no Visual Studio e publicação em Aplicativos Web do Serviço de Aplicativo do Azure (configurar uma vez, depurar e publicar a qualquer momento)  

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>O que você precisa
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

É necessário o seguinte para concluir este tutorial:

* Uma implantação do AD FS local (para uma explicação de ponta a ponta do laboratório de teste neste tutorial, consulte: [Laboratório de teste: STS autônomo com o AD FS na VM do Azure (apenas para teste)](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/))
* Permissões para criar o objeto de confiança de terceira parte confiável no gerenciamento do AD FS
* Visual Studio 2013 Atualização 4 ou posterior
* [SDK do Azure 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou posterior

<a name="bkmk_sample"></a>

## <a name="use-sample-application-for-line-of-business-template"></a>Usar o aplicativo de exemplo para modelo de linha de negócios
O aplicativo de exemplo neste tutorial, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), foi criado pela equipe do Active Directory do Azure. Como o AD FS dá suporte ao Web Services Federation, é possível usá-lo como um modelo para criar aplicativos de linha de negócios com facilidade. Ele tem as seguintes vantagens:

* Usa [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) para autenticar com uma implantação do AD FS local
* Funcionalidade de entrada e saída
* Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (em vez do Windows Identity Foundation), que é o futuro do ASP.NET e é muito mais simples de configurar para autenticação e autorização do que o WIF

<a name="bkmk_setup"></a>

## <a name="set-up-the-sample-application"></a>Configurar o aplicativo de exemplo
1. Clone ou baixe a solução de exemplo em [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) para seu diretório local.
   
   > [!NOTE]
   > As instruções em [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) mostram como configurar o aplicativo com o Azure Active Directory. Mas, neste tutorial, você pode configurá-lo com o AD FS, portanto, siga estas etapas.
   > 
   > 
2. Abra a solução e abra Controllers\AccountController.cs no **Gerenciador de Soluções**.
   
   Você verá que o código simplesmente emite um desafio de autenticação para autenticar o usuário usando o WS-Federation. Todas as autenticações são configuradas em App_Start\Startup.Auth.cs.
3. Abra App_Start\Startup.Auth.cs. No método `ConfigureAuth`, observe a linha:
   
       app.UseWsFederationAuthentication(
           new WsFederationAuthenticationOptions
           {
               Wtrealm = realm,
               MetadataAddress = metadata                                      
           });
   
   No mundo OWIN, este trecho é realmente o mínimo necessário para configurar a autenticação do WS-Federation. Ele é mais simples e mais elegante que o WIF, em que o Web.config é injetado com XML em todos os lugares. A única informação que você precisa é o identificador da terceira parte confiável (RP) e a URL do arquivo de metadados do serviço do AD FS. Aqui está um exemplo:
   
   * Identificador RP: `https://contoso.com/MyLOBApp`
   * Endereço de metadados: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
4. No App_Start\Startup.Auth.cs, altere as seguintes definições de cadeia de caracteres estática:  
   
   <pre class="prettyprint">
   private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
   <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
   <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
   <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
   <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
   
   <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
   </pre>
5. Agora, faça as alterações correspondentes no Web.config. Abra o Web.config e modifique as seguintes configurações de aplicativo:  
   
   <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
   &lt;add key="webpages:Enabled" value="false" /&gt;
   &lt;add key="ClientValidationEnabled" value="true" /&gt;
   &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
   <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
   <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
   <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>
   
   &lt;/appSettings&gt;
   </pre>
   
   Preencha os valores de chave com base em seu respectivo ambiente.
6. Compile o aplicativo para verificar se não existem erros.

É isso. Agora o aplicativo de exemplo está pronto para funcionar com o AD FS. Você ainda precisa configurar uma relação de confiança RP com esse aplicativo no AD FS.

<a name="bkmk_deploy"></a>

## <a name="deploy-the-sample-application-to-azure-app-service-web-apps"></a>Implantar o aplicativo de exemplo para os Aplicativos Web do Serviço de Aplicativo do Azure
Aqui, você publica o aplicativo para um aplicativo Web nos Aplicativos Web do Serviço de Aplicativo, preservando o ambiente de depuração. Observe que você pretende publicar o aplicativo antes que ele tenha uma relação de confiança RP com o AD FS, para que autenticação ainda não funcione. No entanto, se fizer isso agora, você poderá ter a URL do aplicativo Web que também usará para configurar a relação de confiança RP mais tarde.

1. Clique duas vezes com o botão direito em seu projeto e selecione **Publicar**.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)
2. Clique em **Serviço de Aplicativo do Microsoft Azure**.
3. Se você não tiver se inscrito no Azure, clique em **Entrar** e use a conta da Microsoft para sua assinatura do Azure para entrar.
4. Depois de conectado, clique em **Novo** para criar um aplicativo Web.
5. Preencha todos os campos obrigatórios. Você pretende se conectar a dados locais posteriormente, então não crie um banco de dados para este aplicativo Web.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)
6. Clique em **Criar**. Depois que o aplicativo Web for criado, a caixa de diálogo Publicar Web será aberta.
7. Em **URL de destino**, altere **http** para **https**. Copie a URL inteira em um editor de texto para uso posterior. Em seguida, clique em **Publicar**.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)
8. No Visual Studio, abra **Web.Release.config** em seu projeto. Insira o seguinte XML na marca `<configuration>` e substitua o valor de chave por sua URL do aplicativo Web de publicação.  
   
   <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;/appSettings&gt;</pre>

Quando terminar, você terá dois identificadores RP configurados em seu projeto, um para o seu ambiente de depuração no Visual Studio e um para o aplicativo Web publicado no Azure. Você configurará uma relação de confiança RP para cada um dos dois ambientes do AD FS. Durante a depuração, as configurações do aplicativo em Web.config são usadas para fazer sua configuração de **Depuração** funcionar com o AD FS. Quando ele é publicado (por padrão, a configuração **Versão** é publicada), um Web.config transformado é carregado e incorpora as alterações de configuração do aplicativo em Web.Release.config.

Se você deseja anexar o aplicativo Web publicado no Azure ao depurador (ou seja, você deve carregar símbolos de depuração do seu código no aplicativo Web publicado), você pode criar um clone da configuração de depuração para depuração do Azure, mas com sua própria transformação de Web.config (por exemplo, Web.AzureDebug.config) personalizada que usa as configurações do aplicativo de Web.Release.config. Isso permite que você mantenha uma configuração estática em diferentes ambientes.

<a name="bkmk_rptrusts"></a>

## <a name="configure-relying-party-trusts-in-ad-fs-management"></a>Configurar a terceira parte confiável no gerenciamento do AD FS
Agora você precisa configurar uma relação de confiança de RP no Gerenciamento do AD FS antes de poder usar seu aplicativo de exemplo realmente se autenticar no AD FS. Você precisará configurar duas relações de confiança RP separadas, uma para o seu ambiente de depuração e outra para seu aplicativo Web publicado.

> [!NOTE]
> Repita as etapas abaixo para os dois ambientes.
> 
> 

1. No servidor do AD FS, faça logon com credenciais que tenham direitos de gerenciamento para o AD FS.
2. Abra o gerenciamento do AD FS. Clique com o botão direito do mouse em **AD FS\Relacionamentos confiáveis\Objetos de confiança de terceira parte confiável** e selecione **Adicionar objeto de confiança de terceira parte confiável**.
   
   ![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)
3. Na página **Selecionar fonte de dados**, selecione **Inserir dados sobre a terceira parte confiável manualmente**. 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)
4. Na página **Especificar nome de exibição**, digite um nome de exibição para o aplicativo e clique em **Avançar**.
5. Na página **Escolher protocolo**, clique em **Avançar**.
6. Na página **Configurar certificado**, clique em **Avançar**.
   
   > [!NOTE]
   > Como você já deve usar HTTPS, os tokens criptografados são opcionais. Se você realmente deseja criptografar tokens do AD FS nesta página, você também deve adicionar lógica de descriptografia de token em seu código. Para obter mais informações, consulte [Configuração manual de middleware OWIN WS-Federation e aceitação de tokens criptografados](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
   > 
   > 
7. Antes de passar para a próxima etapa, você precisa de uma informação do seu projeto do Visual Studio. Nas propriedades do projeto, observe a **URL do SSL** do aplicativo. 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)
8. De volta no gerenciamento do AD FS, a página **Configurar URL** de **Adicionar assistente de objeto de confiança de terceira parte confiável**, selecione **Habilitar o suporte para o protocolo Web Services Federation Passive** e digite a URL do SSL do seu projeto do Visual Studio que você anotou na etapa anterior. Em seguida, clique em **Avançar**.
   
   ![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)
   
   > [!NOTE]
   > A URL especifica para onde enviar o cliente após a autenticação bem-sucedida. Para o ambiente de depuração, deve ser <code>https://localhost:&lt;port&gt;/</code>. Para o aplicativo Web publicado, deve ser a URL do aplicativo Web.
   > 
   > 
9. Na página **Configurar identificadores**, verifique se o seu projeto de URL de SSL já está listado e clique em **Avançar**. Clique em **Avançar** até o final do assistente com as seleções padrão.
   
   > [!NOTE]
   > No App_Start\Startup.Auth.cs do projeto do Visual Studio, esse identificador é comparado com o valor de <code>WsFederationAuthenticationOptions.Wtrealm</code> durante a autenticação federada. Por padrão, a URL do aplicativo da etapa anterior é adicionada como um identificador RP.
   > 
   > 
10. Agora você concluiu a configuração do aplicativo de RP para seu projeto no AD FS. Em seguida, configure esse aplicativo para enviar as declarações necessitadas para seu aplicativo. A caixa de diálogo **Editar regras de declaração** é aberta por padrão para você no final do assistente para que você possa começar imediatamente. Vamos configurar pelo menos as seguintes declarações (com esquemas entre parênteses):
    
    * Nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) – utilizado pelo ASP.NET para hidratar `User.Identity.Name`.
    * Nome UPN (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) – usado para identificar de forma exclusiva os usuários da organização.
    * Agrupar associações como funções (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) – pode ser usado com a decoração `[Authorize(Roles="role1, role2,...")]` para autorizar controladores/ações. Na realidade, talvez essa abordagem não tenha o melhor desempenho para autorização de função. Se os usuários do AD pertencerem a centenas de grupos de segurança, eles se tornarão centenas de declarações de função no token SAML. Uma abordagem alternativa é enviar uma declaração de função única condicionalmente dependendo da participação do usuário em um grupo específico. No entanto, vamos manter isso simples para este tutorial.
    * ID do nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) – pode ser usado para validação antifalsificação. Para obter mais informações sobre como fazê-lo funcionar com a validação antifalsificação, consulte a seção **Adicionar funcionalidade de linha de negócios** de [Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).
    
    > [!NOTE]
    > Os tipos de declaração que você precisa configurar para seu aplicativo são determinados pelas necessidades do seu aplicativo. Para obter a lista de declarações com suporte por aplicativos do Active Directory do Azure (ou seja, relações de confiança RP), por exemplo, consulte [Token e tipos de declaração com suporte](http://msdn.microsoft.com/library/azure/dn195587.aspx).
    > 
    > 
11. Na caixa de diálogo Editar regras de declaração, clique em **Adicionar regra**.
12. Configure as declarações de nome, UPN e função, conforme mostra a captura de tela e clique em **Concluir**.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)
    
    Em seguida, crie uma declaração de ID de nome temporário usando as etapas demonstradas em [Identificadores de nome em asserções SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).
13. Clique em **Adicionar regra** novamente.
14. Selecione **Enviar Declarações Usando uma Regra Personalizada** e clique em **Avançar**.
15. Cole o seguinte idioma da regra na caixa **Regra personalizada**, nomeie a regra **Por identificador de sessão** e clique em **Concluir**.  
    
    <pre class="prettyprint">
    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
     => add(
         store = "_OpaqueIdStore",
         types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
         query = "{0};{1};{2};{3};{4}",
         param = "useEntropy",
         param = c1.Value,
         param = c1.OriginalIssuer,
         param = "",
         param = c2.Value);
    </pre>
    
    A regra personalizada deve parecer com esta captura de tela:
    
    ![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)
16. Clique em **Adicionar regra** novamente.
17. Selecione **Transformar uma declaração de entrada** e clique em **Avançar**.
18. Configure a regra conforme mostra a captura de tela (usando o tipo de declaração que você criou na regra personalizada) e clique em **Concluir**.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)
    
    Para obter informações detalhadas sobre as etapas para a declaração de ID de nome temporário, consulte [Identificadores de nome em asserções SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).
19. Clique em **Aplicar** na caixa de diálogo **Editar regras de declaração**. Agora deve se parecer com a captura de tela abaixo:
    
    ![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)
    
    > [!NOTE]
    > Novamente, certifique-se de repetir essas etapas para seu ambiente de depuração e o aplicativo Web publicado.
    > 
    > 

<a name="bkmk_test"></a>

## <a name="test-federated-authentication-for-your-application"></a>Teste a autenticação federada em seu aplicativo
Você está pronto para testar a lógica de autenticação do aplicativo com o AD FS. No meu ambiente de laboratório do AD FS, tenho um usuário de teste que pertence a um grupo de teste no Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Para testar a autenticação no depurador, agora basta digitar `F5`. Se você quiser testar a autenticação no aplicativo Web publicado, navegue até a URL.

Depois que o aplicativo Web for carregado, clique em **Entrar**. Você verá uma caixa de diálogo de logon ou a página de logon servidas por AD FS, dependendo do método de autenticação escolhido pelo AD FS. Aqui está o que acontece no Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Depois de fazer logon com um usuário no domínio do AD de implantação do AD FS, você deverá ver a página inicial novamente com **Olá, <User Name>!** no canto. Aqui está o que acontece.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Até agora, você já teve êxito das seguintes maneiras:

* Seu aplicativo atingiu com êxito o AD FS e um identificador RP correspondente foi encontrado no banco de dados do AD FS
* O AD FS foi autenticou com êxito um usuário do AD e redirecionou você de que volta à página inicial do aplicativo
* O AD FS enviou com êxito a declaração de nome (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) ao seu aplicativo, conforme indicado pelo fato de que o nome de usuário é exibido no canto. 

Se a declaração de nome estiver ausente, você teria visto **Olá, !**. Se você observar Views\Shared\\_LoginPartial.cshtml, descobrirá que ele usa `User.Identity.Name` para exibir o nome de usuário. Conforme mencionado anteriormente, se a declaração de nome do usuário autenticado estiver disponível no token SAML, o ASP.NET recuperará essa propriedade. Para ver todas as declarações enviadas pelo AD FS, coloque um ponto de interrupção em Controllers\HomeController.cs, no método de ação de Índice. Depois que o usuário for autenticado, inspecione a coleção `System.Security.Claims.Current.Claims` .

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>

## <a name="authorize-users-for-specific-controllers-or-actions"></a>Autorizar usuários para controladores específicos ou ações
Como incluiu associações de grupo como declarações de função em sua configuração de confiança RP, agora você pode usá-las diretamente na decoração `[Authorize(Roles="...")]` para controladores e ações. Em um aplicativo de linha de negócios com o padrão Criar-Ler-Atualizar-Excluir (CRUD), é possível autorizar funções específicas para acessar cada ação. Por enquanto, você simplesmente testará esse recurso no controlador Início existente.

1. Abra Controllers\HomeController.cs.
2. Decore os métodos de ação `About` e `Contact` e similares ao código abaixo, usando as associações de grupo de segurança que seu usuário autenticado tem.  
   
    <pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
    {
        ViewBag.Message = "Your application description page.";
   
        return View();
    }
   
    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
    {
        ViewBag.Message = "Your contact page.";
   
        return View();
    }
    </pre>
   
    Como adicionei **Usuário de Teste** ao **Grupo de Teste** em meu ambiente de laboratório do AD FS, usarei o Grupo de Teste para testar a autorização em `About`. Para `Contact`, testarei o caso negativo de **Admins. do domínio**, ao qual o **Usuário de Teste** não pertence.
3. Inicie o depurador digitando `F5` , entre e, depois, clique em **Sobre**. Você deverá ver agora a página `~/About/Index` com êxito, se o usuário autenticado for autorizado para essa ação.
4. Agora clique em **Contato**, que, em meu caso, não deve autorizar **Usuário de Teste** para a ação. No entanto, o navegador é redirecionado para o AD FS, que, por fim, mostra esta mensagem:
   
    ![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)
   
    Se examinar esse erro no Visualizador de eventos no servidor do AD FS, você verá esta mensagem de exceção:  
   
    <pre class="prettyprint">
    Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
       at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
       at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
       at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
       at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
    </pre>
   
    O motivo desse erro é que, por padrão, o MVC retorna um 401 Não autorizado quando uma função do usuário não está autorizada. Isso dispara uma solicitação de nova tentativa de autenticação para seu provedor de identidade (AD FS). Uma vez que o usuário já está autenticado, o AD FS retorna para a mesma página, o que, em seguida, emite outro 401, criando um loop de redirecionamento. Você substituirá o método `HandleUnauthorizedRequest` de AuthorizeAttribute por lógica simples para mostrar algo que faça sentido, em vez de dar continuidade ao loop de redirecionamento.
5. Crie um arquivo no projeto chamado AuthorizeAttribute.cs e cole o código a seguir nele.
   
        using System;
        using System.Web.Mvc;
        using System.Web.Routing;
   
        namespace WebApp_WSFederation_DotNet
        {
            [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
            public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
            {
                protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
                {
                    if (filterContext.HttpContext.Request.IsAuthenticated)
                    {
                        filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
                    }
                    else
                    {
                        base.HandleUnauthorizedRequest(filterContext);
                    }
                }
            }
        }
   
    O código de substituição envia um HTTP 403 (Proibido) em vez de HTTP 401 (Não autorizado) em casos do tipo autenticado mas não autorizado.
6. Execute o depurador novamente com `F5`. Clicar em **Contato** agora mostra uma mensagem de erro mais informativa (embora não atraente):
   
    ![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)
7. Publique o aplicativo nos Aplicativos Web do Serviço de Aplicativo do Azure novamente e teste o comportamento do aplicativo ao vivo.

<a name="bkmk_data"></a>

## <a name="connect-to-on-premises-data"></a>Conectar-se a dados locais
Um motivo pelo qual você desejaria implementar seu aplicativo de linha de negócios com o AD FS, em vez de Active Directory do Azure, são os problemas de conformidade em manter dados organizacionais fora do local. Isso também pode significar que o seu site do Azure deve acessar bancos de dados locais, pois você não tem permissão para usar o [banco de dados SQL](/services/sql-database/) como a camada de dados para seus sites.

Os Aplicativos Web do Serviço de Aplicativo do Azure dão suporte ao acesso a bancos de dados locais com duas abordagens: [Conexões híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes Virtuais](web-sites-integrate-with-vnet.md). Para obter mais informações, consulte [Usando integração de VNET e conexões híbridas com Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Outros recursos
* [Autenticar com o Active Directory local em seu aplicativo do Azure](web-sites-authentication-authorization.md)
* [Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
* [Usar a opção de autenticação organizacional local (ADFS) com ASP.NET no Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
* [Migrar um projeto Web VS2013 do WIF para Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
* [Visão geral dos Serviços de Federação do Active Directory](http://technet.microsoft.com/library/hh831502.aspx)
* [Especificação do WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)


