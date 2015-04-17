<properties 
	pageTitle="Conectar um Aplicativo Móvel a um SaaS empresarial | Centro de Desenvolvimento Móvel" 
	description="Saiba como fazer chamadas para os recursos empresariais, como o SharePoint Online" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="na" 
	services="app-service\mobile"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="mahender"/>

# Conectar seu aplicativo móvel a APIs de SaaS

Neste tutorial, você conectará seu aplicativo móvel a uma solução de software como serviço (SaaS) empresarial.  Você vai atualizar o aplicativo em [Autenticar seu Aplicativo com o Logon Único da Biblioteca do Active Directory] para criar um documento do Word no SharePoint Online sempre que um novo TodoItem for adicionado.

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1
* Uma assinatura ativa do [SharePoint Online]
* Conclusão do tutorial [Autenticar seu aplicativo com Logon Único da Biblioteca de Autenticação do Active Directory].  Você deve usar o locatário fornecido por sua assinatura do SharePoint.

## <a name="configure-permissions"></a>Configurar seu aplicativo para acesso delegado ao SharePoint
Por padrão, o token que você recebe do AAD tem permissões limitadas.  Para acessar um recurso de terceiros ou aplicativo SaaS como o SharePoint Online, você deve permiti-lo explicitamente.

1. Na seção **Active Directory** do [Portal de Gerenciamento do Azure], selecione seu locatário.  Navegue até o aplicativo Web que você criou para o Serviço de Aplicativo.

2. Na guia **Configurar** guia, role a página para baixo para obter as permissões para outra seção de aplicativos.  Selecione **Office 365 SharePoint Online** e conceda a permissão delegada **Editar ou excluir arquivos de usuários**.  Em seguida, clique em **Salvar**.

    ![][1]

Você já configurou o AAD para emitir um token de acesso do SharePoint para o Serviço de Aplicativo.

## <a name="store-credentials"></a>Adicionar informações do SharePoint ao seu Aplicativo Móvel

Para fazer uma chamada para o SharePoint, é necessário especificar os pontos de extremidade que o aplicativo móvel precisa percorrer.  Você também precisa ser capaz de provar a identidade do seu serviço de aplicativo.  Isso é obtido usando um par de ID do cliente e Segredo do cliente.  Você já obteve e armazenou a ID do cliente para o serviço de aplicativo durante a configuração de logon do AAD.  Como essas credenciais são sigilosas, você não deve armazená-las como texto simples em seu código.  Em vez disso, você vai definir esses valores como configurações de aplicativo para nosso site de Código de Aplicativo Móvel.

1. Retorne à guia Aplicativos AAD para seu locatário e selecione o aplicativo Web de seu serviço de aplicativo.

2. Em Configurar, role para baixo para Chaves.  Você obterá um Segredo do cliente gerando uma nova chave.  Observe que depois de criar uma chave e sair da página, não haverá mais como obtê-la no portal novamente.  Mediante a criação, você deve copiar e salvar esse valor em um local seguro.  Selecione uma duração para sua chave, em seguida, clique em salvar e copie o valor resultante.

3. Na seção Código do Aplicativo Móvel do Portal de Gerenciamento, navegue até a guia Configurar e role para baixo até as Configurações do Aplicativo.  Aqui você pode fornecer um par chave-valor para ajudá-lo a ter referências das credenciais necessárias.

* Configure SP_Authority para ser o ponto de extremidade de autoridade para seu locatário AAD.  Esse deve ser o mesmo que o valor de autoridade usado para seu aplicativo cliente.  Ele será da forma `https://login.windows.net/contoso.onmicrosoft.com`

* Configure SP_ClientSecret para ser o valor do segredo do cliente que você obteve anteriormente.

* Configure SP_SharePointURL para ser a URL de seu site do SharePoint.  Ele deve ser da forma `https://contoso-my.sharepoint.com`

Você poderá obter esses valores novamente no seu código usando ApiServices.Settings.

## <a name="obtain-token"></a>Obter um token de acesso e chamar a API do SharePoint

Para acessar o SharePoint, é necessário ter um token de acesso especial com o SharePoint como o público de destino.  Para obter esse token, será necessário chamar novamente o AAD com a identidade do Serviço de Aplicativo e o token emitido para o usuário.

1. Abra o projeto de Código de Aplicativo Móvel no Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. No projeto de Código de Aplicativo Móvel, crie uma nova classe chamada SharePointUploadContext.  Nela, adicione o seguinte:

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
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Agora crie um método para adicionar o arquivo à biblioteca de documento do usuário:

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

Para criar um documento no Word, você usará o pacote OpenXML NuGet.  Instale esse pacote abrindo o NuGet Manager e pesquisando por DocumentFormat.OpenXml.

1. Adicione o seguinte código ao TodoItemController.  Isso criará um documento do Word com base em um TodoItem.  O texto do documento será o nome do item.

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

2. Substitua PostTodoItem pelo seguinte:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            
            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);
            
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Testar o aplicativo

1. Publique as alterações ao back-end, depois execute seu aplicativo cliente.  Faça logon quando solicitado e insira um novo TodoItem.

2. Navegue para seu site SharePoint e faça logon com o mesmo usuário.

3. Selecione a guia OneDrive.  Na pasta Documentos, você deve ver um documento do Word com um título GUID.  Ao abri-lo, deverá encontrar o texto de seu TodoItem.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Visualizar o Portal de Gerenciamento do Azure]: https://portal.azure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/pt-br/sharepoint/
[Autenticar o seu aplicativo com logon único da biblioteca de autenticação do Active Directory]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md

<!--HONumber=49-->