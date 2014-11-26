<properties linkid="mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user" urlDisplayName="Access SharePoint on behalf of the user" pageTitle="Access SharePoint on behalf of the user | Mobile Dev Center" metaKeywords="" description="Learn how to make calls to SharePoint on behalf of the user" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Access SharePoint on behalf of the user" authors="mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Acessar o SharePoint em nome do usuário

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este t&oacute;pico mostra como acessar as APIs do SharePoint em nome do usu&aacute;rio registrado atualmente.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial. No v&iacute;deo, Mat Velloso explica como atualizar um aplicativo da Windows Store para interagir com o SharePoint Online.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">12:51:00</span></div>

</div>

Neste tutorial, você atualizará o aplicativo por meio do tutorial Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory, para criar um documento do Word no SharePoint Online quando um novo TodoItem for adicionado.

Este tutorial explica estas etapas básicas para habilitar o acesso em nome de outra pessoa ao SharePoint:

1.  [Registrar seu aplicativo para acesso delegado ao SharePoint][Registrar seu aplicativo para acesso delegado ao SharePoint]
2.  [Adicionar informações do SharePoint ao seu serviço móvel][Adicionar informações do SharePoint ao seu serviço móvel]
3.  [Obter um token de acesso e chamar a API do SharePoint][Obter um token de acesso e chamar a API do SharePoint]
4.  [Criar e carregar um documento do Word][Criar e carregar um documento do Word]
5.  [Testar o aplicativo][Testar o aplicativo]

Este tutorial exige o seguinte:

-   Visual Studio 2013 em execução no Windows 8.1
-   Uma assinatura ativa do [SharePoint Online][SharePoint Online]
-   Conclusão do tutorial [Autenticar seu aplicativo com Logon Único da Biblioteca de Autenticação do Active Directory][Autenticar seu aplicativo com Logon Único da Biblioteca de Autenticação do Active Directory]. Você deve usar o locatário fornecido por sua assinatura do SharePoint.

## <a name="configure-permissions"></a>Configurar seu aplicativo para acesso delegado ao SharePoint

Por padrão, o token que você recebe do AAD tem permissões limitadas. Para acessar um recurso de terceiros ou aplicativo SaaS como o SharePoint Online, você deve permiti-lo explicitamente.

1.  Na seção **Active Directory** do [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], selecione seu locatário. Navegue para o aplicativo Web que você criou para o serviço móvel.

    ![][0]

2.  Na guia **Configurar**, role a página para baixo para obter as permissões para outra seção de aplicativos. Selecione **Office 365 SharePoint Online** e conceda a permissão delegada para **Editar e excluir arquivos dos usuários**. Em seguida, clique em **Salvar**.

    ![][1]

Agora você configurou o AAD para emitir um token de acesso do SharePoint ao serviço móvel.

## <a name="store-credentials"></a>Adicionar informações do SharePoint ao seu serviço móvel

Para fazer uma chamada para o SharePoint, é necessário especificar os pontos de extremidade que o serviço móvel precisa percorrer. Também é necessário poder provar a identidade de seu serviço móvel. Isso é obtido usando um par de ID do cliente e Segredo do cliente. Você já obteve e guardou a ID do cliente para o serviço móvel durante a configuração de logon do AAD. Como essas credenciais são sigilosas, você não deve armazená-las como texto simples em seu código. Em vez disso, você definirá esses valores como Configurações do Aplicativo em nosso Serviço Móvel.

1.  Retorne à guia de Aplicativos AAD de seu locatário e selecione o aplicativo Web de seu serviço móvel.

2.  Em Configurar, role para baixo para Chaves. Você obterá um Segredo do cliente gerando uma nova chave. Observe que depois de criar uma chave e sair da página, não haverá mais como obtê-la no portal novamente. Mediante a criação, você deve copiar e salvar esse valor em um local seguro. Selecione uma duração para sua chave, em seguida, clique em salvar e copie o valor resultante.

    ![][2]

3.  Na seção de Serviços móveis do Portal de Gerenciamento, navegue para a guia Configurar e role para baixo, para as Configurações do aplicativo. Aqui você pode fornecer um par de valor chave para ajudá-lo a ter referências das credenciais necessárias.

    ![][3]

4.  Configure SP\_Authority para ser o ponto de extremidade de autoridade para seu locatário AAD. Esse deve ser o mesmo que o valor de autoridade usado para seu aplicativo cliente. Ele estará no formulário <https://login.windows.net/contoso.onmicrosoft.com>

5.  Configure SP\_ClientSecret para ser o valor do segredo do cliente que você obteve anteriormente.

6.  Configure SP\_SharePointURL para ser a URL de seu site do SharePoint. Ela deve estar no formulário <https://contoso-my.sharepoint.com>

Você poderá obter esses valores novamente em seu código, usando ApiServices.Settings.

## <a name="obtain-token"></a>Obter um token de acesso e chamar a API do SharePoint

Para acessar o SharePoint, é necessário ter um token de acesso especial com o SharePoint como o público de destino. Para obter esse token, será necessário chamar novamente o AAD com a identidade do Serviço Móvel e o token que foi emitido para o usuário.

1.  Abra o seu projeto de back-end de Serviços Móveis no Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

1.  Em seu projeto de back-end de Serviços Móveis, crie uma nova classe chamada SharePointUploadContext. Nela, adicione o seguinte:

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new UserAssertion(userToken), new ClientCredential(clientId, clientSecret));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

2.  Agora crie um método para adicionar o arquivo à biblioteca de documento do usuário:

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Criar e carregar um documento do Word

Para criar um documento no Word, você usará o pacote OpenXML NuGet. Instale esse pacote abrindo o NuGet Manager e pesquisando por DocumentFormat.OpenXml.

1.  Adicione o seguinte código ao TodoItemController. Isso criará um documento do Word com base em um TodoItem. O texto do documento será o nome do item.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2.  Substitua PostTodoItem pelo seguinte:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Testar o aplicativo

1.  Publique as alterações ao back-end, depois execute seu aplicativo cliente. Faça logon quando solicitado e insira um novo TodoItem.

2.  Navegue para seu site SharePoint e faça logon com o mesmo usuário.

3.  Selecione a guia OneDrive. Na pasta Documentos, você deve ver um documento do Word com um título GUID. Ao abri-lo, deverá encontrar o texto de seu TodoItem.

    ![][4]





  [Registrar seu aplicativo para acesso delegado ao SharePoint]: #configure-permissionss
  [Adicionar informações do SharePoint ao seu serviço móvel]: #store-credentials
  [Obter um token de acesso e chamar a API do SharePoint]: #obtain-token
  [Criar e carregar um documento do Word]: #create-document
  [Testar o aplicativo]: #test-application
  [SharePoint Online]: http://office.microsoft.com/pt-br/sharepoint/
  [Autenticar seu aplicativo com Logon Único da Biblioteca de Autenticação do Active Directory]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
  [1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
  [2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
  [3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
  [4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png
