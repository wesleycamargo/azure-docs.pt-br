---
title: "Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory | Microsoft Docs"
description: "Saiba como criar um aplicativo de linha de negócios ASP.NET MVC no Serviço de Aplicativo do Azure que realiza a autenticação com o Azure Active Directory"
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 2576b658eaf1df95aa9700e06559edf6066cc534
ms.contentlocale: pt-br
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory
Este artigo mostra como criar um aplicativo de linha de negócios do .NET nos [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando o recurso [Autenticação/Autorização](../app-service/app-service-authentication-overview.md). Também mostra como usar a [API do Graph do Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para consultar dados de diretório no aplicativo.

O locatário do Azure Active Directory que você usa pode ser um diretório somente do Azure. Ou, pode ser [sincronizado com o Active Directory local](../active-directory/active-directory-aadconnect.md) para criar uma experiência de logon único para trabalhadores locais e remotos. Este artigo usa o diretório padrão para sua conta do Azure.

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>O que você compilará
Você compilará um aplicativo simples de linha de negócios CRUD (Create-Read-Update-Delete) nos Aplicativos Web do Serviço de Aplicativo que acompanha itens de trabalho com os seguintes recursos:

* Autentica usuários no Active Directory do Azure
* Consulta usuários de diretório e grupos usando a [API do Graph do Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* Usar o modelo *Sem Autenticação* do ASP.NET MVC

Se você precisar de controle de acesso baseado em função (RBAC) para seu aplicativo de linha de negócios no Azure, confira a [Próxima etapa](#next).

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>O que você precisa
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

É necessário o seguinte para concluir este tutorial:

* Um locatário do Active Directory do Azure com usuários em vários grupos
* Permissões para criar aplicativos no locatário do Active Directory do Azure
* Visual Studio 2013 Atualização 4 ou posterior
* [SDK 2.8.1 do Azure ou posterior](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Crie e implante um aplicativo Web no Azure
1. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
2. Selecione **Aplicativo Web ASP.NET**, nomeie seu projeto e clique em **OK**.
3. Escolha o modelo **MVC** e altere a autenticação para **Sem Autenticação**. Certifique-se de que a opção **Hospedar na Nuvem** esteja selecionada e clique em **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Adicionar uma conta** (e, depois, **Adicionar uma conta** na lista suspensa) para fazer logon em sua conta do Azure.
5. Depois de conectado, configure seu aplicativo Web. Crie um grupo de recursos e um novo Plano do Serviço de Aplicativo clicando no respectivo botão **Novo** . Clique em **Explorar serviços adicionais do Azure** para continuar.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. Na guia **Serviços**, clique em **+** para adicionar um Banco de Dados SQL para seu aplicativo. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. Em **Configurar Banco de Dados SQL**, clique em **Novo** para criar uma instância do SQL Server.
8. Em **Configurar SQL Server**, configure sua instância do SQL Server. Em seguida, clique em **OK**, **OK** e em **Criar** para iniciar a criação do aplicativo no Azure.
9. Em **Atividade do Serviço de Aplicativo do Azure**, você pode ver quando a criação do aplicativo terminar. Clique em **Publicar&lt;*nome_do_aplicativo*>para esse Aplicativo Web agora** e, em seguida, clique em **Publicar**. 
   
    Após a conclusão do Visual Studio, ele abrirá o aplicativo de publicação no navegador. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>Configurar a autenticação e acesso ao diretório
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. No meu à esquerda, clique em **Serviços de Aplicativos** > **&lt;*nome_do_aplicativo*>** > **Autenticação/Autorização**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. Ative a autenticação do Azure Active Directory clicando em **Ativar** > **Azure Active Directory** > **Expresso** > **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. Clique em **Salvar** na barra de comandos.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    Após a gravação das configurações de autenticação, tente navegar até seu aplicativo novamente no navegador. As configurações padrão impõem a autenticação em todo o aplicativo. Se você ainda não estiver conectado, será redirecionado para uma tela de logon. Depois de conectado, você verá seu aplicativo protegido por HTTPS. Em seguida, será necessário habilitar o acesso aos dados do diretório. 
5. Navegue até o [Portal Clássico](https://manage.windowsazure.com).
6. No menu à esquerda, clique em **Active Directory** > **Diretório Padrão** > **Aplicativos** > **&lt;*nome_do_aplicativo*>**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    Este é o aplicativo do Azure Active Directory criado pelo Serviço de Aplicativo para você a fim de habilitar o recurso de Autorização/Autenticação.
7. Clique em **Usuários** e **Grupos** para se certificar de que haja alguns usuários e grupos no diretório. Caso contrário, crie alguns usuários e grupos de teste.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. Clique em **Configurar** para configurar esse aplicativo.
9. Role para baixo até a seção **Chaves** e adicione uma chave selecionando uma duração. Em seguida, clique em **Permissões Delegadas** e selecione **Ler dados do diretório**. 
   Clique em **Salvar**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. Depois que as configurações forem salvas, role para cima até a seção **Chaves** e clique no botão **Copiar** para copiar a chave do cliente. 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > Se você sair desta página agora, não poderá acessar essa chave de cliente novamente.
    > 
    > 
11. Em seguida, você precisará configurar seu aplicativo Web com essa chave. Faça logon no [Azure Resource Manager](https://resources.azure.com) com sua conta do Azure.
12. Na parte superior da página, clique em **Leitura/Gravação** para fazer alterações no Azure Resource Manager.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. Encontre as configurações de autenticação de seu aplicativo, localizadas em assinaturas > **&lt;*nome_da_assinatura*>** > **resourceGroups** > **&lt;*nome_do_grupo_de_recursos*>** > **provedores** > **Microsoft.Web** > **sites** > **&lt;*nome_do_aplicativo*>** > **config** > **authsettings**.
14. Clique em **Editar**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. No painel de edição, defina as propriedades `clientSecret` e `additionalLoginParams` da seguinte maneira.
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. Clique em **Inserir** na parte superior para enviar suas alterações.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. Agora, para testar se você tem o token de autorização para acessar a API do Graph do Azure Active Directory, basta navegar até **https://&lt;*nome_do_aplicativo*>.azurewebsites.net/.auth/me** no seu navegador. Se você configurou tudo corretamente, veja a propriedade `access_token` na resposta JSON.
    
    O caminho da URL de `~/.auth/me` é gerenciado pela Autenticação/Autorização do Serviço de Aplicativo para fornecer todas as informações relacionadas à sua sessão autenticada. Para saber mais, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).
    
    > [!NOTE]
    > O caminho da URL de `access_token` tem um período de expiração. No entanto, a Autenticação/Autorização do Serviço de Aplicativo fornece a funcionalidade de atualização do token com `~/.auth/refresh`. Para saber mais sobre como usá-la, consulte [Repositório de Token do Serviço de Aplicativo](https://cgillum.tech/2016/03/07/app-service-token-store/).
    > 
    > 

Em seguida, você fará algo útil com os dados do diretório.

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>Adicionar uma funcionalidade de linha de negócios ao seu aplicativo
Agora, crie um rastreador de itens de trabalho CRUD simples.  

1. Na pasta ~\Modelos, crie um arquivo de classe chamado WorkItem.cs e substitua `public class WorkItem {...}`pelo código a seguir:
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. Compile o projeto para disponibilizar o novo modelo para a lógica de scaffolding no Visual Studio.
3. Adicione um novo item com scaffold `WorkItemsController` à pasta ~\Controllers (clique com o botão direito do mouse em **Controladores**, aponte para **Adicionar** e selecione **Novo item com scaffold**). 
4. Selecione **Controlador MVC 5 com modos de exibição usando o Entity Framework** e clique em **Adicionar**.
5. Escolha o modelo que você criou e clique em **+** e em **Adicionar** para adicionar um contexto de dados. Em seguida, clique em **Adicionar**.
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. Em ~\Views\WorkItems\Create.cshtml (um item automaticamente submetido a scaffolding), localize o método auxiliar `Html.BeginForm` e faça as seguintes alterações destacadas:  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   Observe que `token` e `tenant` são usados pelo objeto `AadPicker` para fazer chamadas da API do Graph do Azure Active Directory. Você adicionará `AadPicker` mais tarde.     
   
   > [!NOTE]
   > Você também pode obter `token` e `tenant` do lado do cliente com `~/.auth/me`, mas isso seria uma chamada de servidor adicional. Por exemplo:
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. Faça as mesmas alterações com ~\Views\WorkItems\Edit.cshtml.
8. O objeto `AadPicker` é definido em um script ao qual você precisa adicionar ao seu projeto. Clique com o botão direito do mouse na pasta ~\Scripts, aponte para **Adicionar** e clique em **arquivo JavaScript**. Digite `AadPickerLibrary` para o nome de arquivo e clique em **OK**.
9. Copie o conteúdo deste [local](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) em ~\Scripts\AadPickerLibrary.js.
   
   No script, o objeto `AadPicker` chama a [API do Graph do Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para pesquisar usuários e grupos que correspondam à entrada.  
10. ~\Scripts\AadPickerLibrary.js também usa o [widget Autocomplete do jQuery UI](https://jqueryui.com/autocomplete/). Portanto, você precisa adicionar o jQuery UI ao seu projeto. Clique com o botão direito do mouse em seu projeto e clique em **Gerenciar Pacotes NuGet**.
11. No Gerenciador de Pacotes NuGet, clique em Procurar, digite **jquery-ui** na barra de pesquisa e clique em **jQuery.UI.Combined**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. No painel à direita, clique em **Instalar** e em **OK** para continuar.
13. Abra ~\App_Start\BundleConfig.cs e faça as seguintes alterações realçadas:  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    Há outras maneiras de alto desempenho para gerenciar arquivos JavaScript e CSS em seu aplicativo. No entanto, para manter a simplicidade, apenas acumule os pacotes carregados com cada exibição.
14. Por fim, em ~\Global.asax, adicione a linha de código a seguir ao método `Application_Start()`. `Ctrl`+`.` em cada erro de resolução de nomes para corrigi-lo.
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > Você precisa dessa linha de código porque o modelo padrão de MVC usa a decoração <code>[ValidateAntiForgeryToken]</code> em algumas das ações. Devido ao comportamento descrito por [Brock Allen](https://twitter.com/BrockLAllen) em [MVC 4, AntiForgeryToken e declarações](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), seu HTTP POST poderá falhar na validação do token antifalsificação pelos seguintes motivos:
    > 
    > * O Azure Active Directory não envia http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que é exigido por padrão pelo token antifalsificação.
    > * Se o Azure Active Directory for um diretório sincronizado com o AD FS, a relação de confiança do AD FS por padrão não envia a declaração http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, embora você possa configurar manualmente o AD FS para enviá-la.
    > 
    > `ClaimTypes.NameIdentifies` especifica a declaração `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que o Active Directory do Azure fornece.  
    > 
    > 
15. Agora, publique suas alterações. Clique com o botão direito do mouse em seu projeto e clique em **Publicar**.
16. Clique em **Configurações**, verifique se há uma cadeia de conexão com seu Banco de Dados SQL, escolha **Atualizar Banco de Dados** para fazer as alterações de esquema no modelo e clique em **Publicar**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. No navegador, navegue até https://&lt;*nome_do_aplicativo*>.azurewebsites.net/workitems e clique em **Criar Novo**.
18. Clique na caixa **AssignedToName** . Agora você verá os usuários e grupos do seu locatário do Azure Active Directory em uma lista suspensa. Você pode digitar para filtrar ou usar a tecla `Up` ou `Down` ou clicar para selecionar o usuário ou grupo. 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. Clique em **Criar** para salvar as alterações. Em seguida, clique em **Editar** no item de trabalho criado para observar o mesmo comportamento.

Parabéns, você está executando um aplicativo de linha de negócios no Azure com acesso ao diretório! Você pode fazer muito mais com a API do Graph. Consulte a [referência da API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>

## <a name="next-step"></a>Próxima etapa
Se você precisar de controle de acesso baseado em função (RBAC) para o aplicativo de linha de negócios no Azure, confira [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) para obter um exemplo da equipe do Azure Active Directory. Ele mostra como habilitar funções para seu aplicativo do Azure Active Directory e, em seguida, autorizar usuários com a decoração `[Authorize]` .

Se o seu aplicativo de linha de negócios precisar acessar dados locais, confira [Acesso a recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Outros recursos
* [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md)
* [Autenticar com o Active Directory local em seu aplicativo do Azure](web-sites-authentication-authorization.md)
* [Criar um aplicativo de linha de negócios no Azure com autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md)
* [Autenticação do Serviço de Aplicativo e a API do Graph do Azure AD](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Exemplos e documentação do Active Directory do Microsoft Azure](https://github.com/AzureADSamples)
* [Tipos de declaração e token com suporte no Active Directory do Azure](http://msdn.microsoft.com/library/azure/dn195587.aspx)

