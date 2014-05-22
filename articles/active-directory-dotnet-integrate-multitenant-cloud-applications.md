<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrando aplicativos multilocatários na nuvem com o Active Directory do Azure" authors="" solutions="" manager="" editor="" />

# Integrando aplicativos multilocatários na nuvem com o Active Directory do Azure

##<a name="introduction"></a>Introdução

O Active Directory do Azure (AD do Azure) é um serviço moderno e baseado em REST que fornece recursos de gerenciamento de identidade e de Controle de Acesso para seus aplicativos de nuvem. O AD do Azure integra-se facilmente com serviços de nuvem, bem como o Azure, o Microsoft Office 365, o Dynamics CRM Online e o Windows Intune. As implantações existentes do Active Directory no local também podem tirar total proveito do AD do Azure. Para saber mais, consulte a [página Identidade][] em [windowsazure.com][]

Este tutorial passo a passo é voltado para desenvolvedores do .NET que desejam integrar aplicativos multilocatários com o AD do Azure. Você saberá como:

* Habilitar os clientes para entrarem em seu aplicativo usando o AD do Azure
* Habilitar SSO (logon único) com o AD do Azure
* Consultar dados de um diretório do cliente usando a Graph API do AD do Azure

O aplicativo de exemplo incluído neste tutorial pode ser [baixado aqui][]. O exemplo pode ser executado sem alterações, mas talvez seja necessário alterar a [atribuição da porta no Visual Studio][] para usar https. Siga as instruções no link, mas defina o protocolo de ligação como "https" na seção vinculações do arquivo applicationHost.config. Todos os trechos de código nas etapas abaixo foram tirados do exemplo.

> [WACOM.NOTE]
> O exemplo de aplicativo de diretório multilocatário é fornecido apenas para fins ilustrativos.  Este exemplo (incluindo suas classes de biblioteca auxiliar) não deve ser usado na produção.


###Pré-requisitos
Os seguintes pré-requisitos de desenvolvedor são necessários para esta explicação passo a passo:

* [Visual Studio 2012][]
* [WCF Data Services para OData][]

###Sumário
* [Introdução][]
* [Parte 1: Obter uma ID de cliente para acesso ao AD do Azure][]
* [Parte 2: Habilitar os clientes para entrarem usando o AD do Azure][]
* [Parte 3: Habilitar Logon Único][]
* [Parte 4: Acessar o AD Graph do Azure][]
* [Parte 5: Publicar seu aplicativo][]
* [Resumo][]


##<a name="getclientid"></a>Parte 1: Obter uma ID de cliente para acesso ao AD do Azure
Esta seção descreve como obter a ID e o Segredo de um cliente depois que você tiver criado uma conta do Painel do Vendedor da Microsoft. Uma ID de Cliente é o identificador exclusivo para seu aplicativo e um Segredo do Cliente é a chave associada necessária ao fazer solicitações usando a ID do Cliente. Ambos são necessários para integrar seu aplicativo com o AD do Azure.

###Etapa 1: Criar uma conta com o Painel do Vendedor da Microsoft
Para desenvolver e publicar aplicativos que se integram com o AD do Azure, você deve se inscrever para uma conta do [Painel do Vendedor da Microsoft][]. Em seguida, você receberá uma solicitação para [criar um perfil de conta][] como uma empresa ou como um indivíduo. Esse perfil é usado para publicar aplicativos no Azure Marketplace ou em outros marketplaces e é necessário para gerar uma ID do Cliente e um Segredo do Cliente.  

Novas contas são colocadas em um estado de "Conta com Aprovação Pendente". Esse estado impede que você inicie o desenvolvimento - você ainda pode criar IDs de Cliente, bem como um rascunho dos detalhes do aplicativo. No entanto, um detalhe de aplicativo só pode ser enviado para aprovação depois que a própria conta for aprovada. O detalhe do aplicativo enviado só ficará visível para os clientes no Azure Marketplace depois que tiver sido aprovado.

###Etapa 2: Obter uma ID do Cliente para seu aplicativo
Uma ID do Cliente e um Segredo do Cliente são necessários para integrar seu aplicativo com o AD do Azure. Uma ID do Cliente é o identificador exclusivo para o seu aplicativo e é usada principalmente para identificar um aplicativo para logon único ou para chamadas de autenticação para o AD Graph do Azure. Para obter mais informações sobre como obter uma ID do Cliente e um Segredo do Cliente, consulte [Criar IDs e Segredos de clientes no Painel do Vendedor da Microsoft][]. 

> [WACOM.NOTE]
> A ID do Cliente e o Segredo do Cliente serão necessários mais tarde neste passo a passo, portanto, você deve registrá-los.

Para gerar uma ID do Cliente e um Segredo do Cliente, será necessário inserir as seguintes propriedades no Painel do Vendedor da Microsoft:

**Domínio do Aplicativo**: o nome do host do seu aplicativo, por exemplo "contoso.com". Essa propriedade não deve conter nenhum número de porta. Durante o desenvolvimento, essa propriedade deve ser definida como "localhost".

**URL de Redirecionamento do Aplicativo**: a URL de redirecionamento para onde o AD do Azure enviará uma resposta depois que o usuário entrar e quando uma organização tiver autorizado o seu aplicativo, por exemplo: "https://contoso.com/". Durante o desenvolvimento, essa propriedade deve ser definida como "https://localhost:&#60;port number&#62;"

###Etapa 3: Configurar seu aplicativo para usar a ID do Cliente e o Segredo do Cliente
Esta etapa requer a ID e o Segredo do Cliente que foram gerados durante a inscrição no Painel do Vendedor. A ID do Cliente é usada para SSO, e a ID e o Segredo do Cliente serão usados mais tarde para obter um token de acesso para chamar a Graph API do AD do Azure.  

O aplicativo de exemplo foi previamente programado para usar o AD do Azure e carrega a ID do Cliente e o Segredo do Cliente da configuração. No arquivo **Web.config** no aplicativo de exemplo, faça as seguintes alterações: 

1. No nó **appSettings**, substitua os valores de "clientId" e "SymmetricKey" por sua ID do Cliente, Segredo do Cliente e seu domínio de aplicativo: 

		<appSettings>
    		<add key="clientId" value="(Your Client ID value)"/>
	    	<add key="SymmetricKey" value="(Your Client Secret value)"/>
			<add key="AppHostname" value="(Your App Domain)"/>
		</appSettings>

2. No nó **audienceUris** de **system.identityModel**, insira seu ID do Cliente após "spn:":

		<system.identityModel>
    		<audienceUris>
            	<add value="spn:(Your Client ID value)" />
    		</audienceUris>


##<a name="enablesignup"></a>Parte 2: Habilitar os clientes para entrarem usando o AD do Azure

Esta seção descreve como permitir que os clientes se inscrevam em seu aplicativo usando o AD do Azure. Antes que um cliente possa usar um aplicativo integrado ao AD do Azure, um administrador de locatários deve autorizar o aplicativo. Esse processo de autorização é iniciado com uma solicitação de consentimento do seu aplicativo no Azure, o que gera uma resposta que deve ser tratada por seu aplicativo. As etapas a seguir descrevem como gerar uma solicitação de consentimento e tratar a resposta.

As etapas desta seção usam as classes auxiliares do aplicativo de exemplo. Essas classes estão contidas na biblioteca *Microsoft.IdentityModel.WAAD.Preview* do exemplo e facilitam focalizar aspectos específicos do código do aplicativo em vez de detalhes de identidade e protocolo.

###Etapa 1: Solicitando consentimento para seu aplicativo
A interação de exemplo a seguir demonstra o processo de solicitação de consentimento para seu aplicativo:

1. O cliente clica em um link "inscrever-se usando o AD do Azure" em uma página da web em seu aplicativo.
2.	Você redireciona o cliente para a página de autorização do AD do Azure com as informações do aplicativo anexadas à solicitação.
3.	O cliente concede ou nega o consentimento para seu aplicativo.
4.	O AD do Azure redireciona o cliente para a URL de redirecionamento do aplicativo especificada. Você especificou essa URL quando você gerou uma ID do Cliente e um Segredo do Cliente no Painel do Vendedor da Microsoft.  A solicitação de redirecionamento indica o resultado da solicitação de consentimento, incluindo informações sobre o seu locatário quando o consentimento é concedido.

Para gerar a solicitação de redirecionamento na etapa 2 acima, você deve acrescentar parâmetros de querystring à seguinte URL à página de autorização do AD do Azure: *http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx*

Os parâmetros querystring são descritos abaixo:

**ApplicationID**: (obrigatório) o valor de **ClientID** recebido no Painel do Vendedor.

**RequestedPermissions**: (opcional) as permissões devem ser concedidas a seu aplicativo pelo locatário.
Durante o desenvolvimento, essas permissões são usadas para testar aplicativos não publicados. Para aplicativos publicados, esse parâmetro será ignorado. Em vez disso, as permissões solicitadas no detalhe de seu aplicativo serão usadas. Consulte a Parte 5 para obter mais informações sobre esse detalhe.
Há três valores possíveis para esse parâmetro:

**DirectoryReader**: concede permissão para ler dados do diretório, como contas de usuário, grupos e informações sobre sua organização. Habilita SSO.

**UserAccountAdministrator**: concede permissão para ler e gravar dados, como usuários, grupos e informações sobre sua organização. Habilita SSO.

**None**: permite logon único mas desabilita o acesso aos dados do diretório.

O valor padrão será "None" se o parâmetro não for especificado ou especificado incorretamente.

A seguir é apresentado um exemplo de uma URL de solicitação de consentimento válida:
*https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader*

No aplicativo de exemplo, o link "Register" contém uma URL semelhante para solicitação de consentimento, conforme mostrado abaixo:

![login][login]


> [WACOM.NOTE]
> Ao testar seu aplicativo não publicado, você passará por uma experiência de consentimento que é semelhante a de seus clientes. No entanto, a página de autorização para um aplicativo não publicado tem uma aparência diferente da página de autorização para um aplicativo publicado. Um aplicativo publicado exibe o nome do aplicativo, o logotipo e os detalhes do publicador, enquanto um aplicativo não publicado não exibe.

###Etapa 2: Manipulando a resposta do consentimento
Depois que um cliente tiver dado ou negado consentimento para o seu aplicativo, o AD do Azure envia uma resposta para a URL de Redirecionamento de seu aplicativo. Essa resposta contém os seguintes parâmetros de querystring:

**TenantId**: o GUID exclusivo do locatário do AD do Azure que autorizou seu aplicativo. Esse parâmetro só poderá ser especificado se o cliente tiver autorizado seu aplicativo.

**Consent**: o valor será definido como "Granted" se o aplicativo foi autorizado ou como "Denied" se a solicitação foi rejeitada.

A seguir é apresentado um exemplo de uma resposta válida para a solicitação de consentimento que indica que o aplicativo foi autorizado:
*https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted*

Seu aplicativo precisa manter o contexto para que a solicitação enviada para a página de autorização do AD do Azure esteja ligada à resposta (e pode rejeitar todas as respostas sem uma solicitação associada).

<div class="dev-callout"><strong>Observação</strong><p>Depois que o consentimento for concedido, o AD do Azure pode levar algum tempo até que o acesso ao SSO e ao Graph sejam provisionados. O primeiro usuário de cada organização que se inscreve em seu aplicativo pode ver erros de entrada até que o provisionamento seja concluído.</p></div>

Depois que um cliente tiver dado consentimento a seu aplicativo, é importante associar e armazenar o locatário recém-criado em seu aplicativo com a TenantId retornada pela resposta de consentimento. O aplicativo de exemplo contém um *HttpModule* no namespace *Microsoft.IdentityModel.WAAD.Preview.Consent* que registra automaticamente a TenantId em um "armazenamento de dados" customer/TenantId em todas as respostas de consentimento com êxito.  O código para isso está incluído abaixo e a gravação de TenantId para um "armazenamento de dados" customer/TenantId é executada pelo método *TrustedIssuers.Add*:

	private void Application_BeginRequest(Object source,
             EventArgs e)
    {
    	HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
        	if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
            	// For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"];	
            }
        }            
    }

Antes de testar o código de solicitação/resposta do consentimento para seu aplicativo, você deve obter um locatário do diretório do AD do Azure.

<h3>Etapa 3: Obter um locatário do AD do Azure para testar seu aplicativo</h3>
Para testar a capacidade de integração do aplicativo com o AD do Azure, você precisará de um locatário do AD do Azure. Se você já tiver um locatário usado para testar outro aplicativo, você poderá reutilizá-lo. É recomendável obter pelo menos dois locatários para garantir que seu aplicativo possa ser testado e usado por vários locatários. Não é recomendável usar um locatário de produção para essa finalidade. [Obter um locatário do AD do Azure][]

Depois que tiver obtido um locatário do AD do Azure, você poderá criar e executar o aplicativo pressionando **F5**. Além disso, você pode tentar se inscrever em seu aplicativo usando o novo locatário. 

<div class="dev-callout"><strong>Observação</strong><p>Se seus clientes se inscreverem para um novo locatário do AD do Azure, pode levar algum tempo para que o locatário seja totalmente provisionado. Os usuários podem ver erros na página de consentimento até que o provisionamento seja concluído.</p></div>

<h2><a name="enablesso"></a>Parte 3: Habilitar Logon Único</h2>

Esta seção mostra como habilitar o SSO (Logon Único). O processo inicia com a construção de uma solicitação de entrada no AD do Azure que autentica um usuário para seu aplicativo, e em seguida, verifica na resposta da entrada se o cliente pertence a um locatário que autorizou seu aplicativo. A solicitação de entrada requer sua ID do Cliente no Painel do Vendedor e a ID do Locatário da organização do cliente.

A solicitação de entrada é específica a um locatário do diretório e deve incluir uma TenantID.  Uma TenantID pode ser determinada em um nome de domínio do locatário do diretório do AD do Azure. Há duas maneiras de obter esse nome de domínio do usuário final enquanto ele entra:

* Se a URL do aplicativo for *https://contoso.myapp.com* ou *https://myapp.com/contoso.com*, *contoso* e *contoso.com* representam o nome do domínio do AD do Azure e *myapp.com* representa a URL do aplicativo. 
* Seu aplicativo pode solicitar o endereço de email ou o nome do domínio do AD do Azure ao usuário. Essa abordagem é usada no aplicativo de exemplo, onde o usuário deve inserir seu nome de domínio do AD do Azure, conforme mostrado abaixo:

![login][login]

<h3>Etapa 1: Procurar a ID do locatário</h3>
Usando o nome de domínio do AD do Azure fornecido pelo cliente, você pode procurar a ID do Locatário do cliente analisando os metadados de federação do AD do Azure. No aplicativo de exemplo, esse processo é tratado pelo método *Domain2TenantId* da classe *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals*.

Para demonstrar esse processo, as etapas a seguir usam o nome do domínio contoso.com.

1.	Obtenha o arquivo **FederationMetadata.xml** do locatário do AD do Azure. Por exemplo:  
*https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml*
2.	No arquivo **FederationMetadata.xml**, localize a entrada **Entity Descriptor**. A ID do Locatário é incluída como parte da propriedade **entityID** e seguida a "https://sts.windows.net", conforme mostrado abaixo:

		 <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 
Nesse caso, o valor de TenantID é **a7456b11-6fe2-4e5b-bc83-67508c201e4b**
3.	No "armazenamento de dados" customer/TenantId do seu aplicativo, você deve armazenar o domínio e a TenantID associada.  Esses dois valores podem ser usados juntos em futuras solicitações de entrada e eliminar a necessidade de obter o **FederationMetadata.xml** em cada solicitação. O aplicativo de exemplo não apresenta essa otimização.

<h3>Etapa 2: Gerar a solicitação de entrada</h3>
Quando um cliente entra em seu aplicativo, como ao clicar em um botão de entrada, a solicitação de entrada deve ser gerada usando a ID do Locatário do cliente e a ID do Cliente do aplicativo No aplicativo de exemplo, essa solicitação é gerada pelo método *GenerateSignInMessage* da classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils*. Esse método verifica se a TenantID do cliente representa uma organização que autorizou seu aplicativo e gera a URL de destino para o botão de entrada, conforme mostrado abaixo:

![login][login]

Um clique no botão levará o navegador do usuário para uma página de entrada do AD do Azure. Depois de conectado, o AD do Azure retornará uma resposta de entrada para o aplicativo.

###Etapa 3: Validar o emissor do token de entrada na resposta da entrada

Quando um cliente entra em seu aplicativo, você precisa validar se seu locatário autorizou seu aplicativo. A resposta de entrada contém um token, e o token contém propriedades e declarações que podem ser inspecionadas por seu aplicativo.

Para executar essa validação, você deve obter a TenantID da propriedade Issuer no token e verificar se ela existe no "armazenamento de dados" customer/TenantId. No aplicativo de exemplo, essa validação é obtida criando uma classe de manipulador de token personalizado que estende o *Saml2SecurityTokenHandler* do Windows Identity Foundation. O manipulador de token personalizado verifica o token de segurança de entrada e torna suas propriedades e declarações disponíveis para o aplicativo para que a TenantID possa ser validada. O trecho de código para essa classe é mostrado abaixo.

No aplicativo de exemplo, o código original pode ser encontrado sob o namespace *Microsoft.IdentityModel.WAAD.Preview.WebSSO*. O manipulador de token também usa o método Contains da classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers*, que verifica se a TenantID é persistida no "armazenamento de dados" customer/TenantId.

	/// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

Assim que o token é validado, o usuário é conectado ao aplicativo. Execute o aplicativo e tente entrar usando uma conta do AD do Azure no locatário consentido que você criou anteriormente.

<h2><a name="accessgraph"></a>Parte 4: Acessar o AD Graph do Azure</h2>

Esta seção descreve como obter um token de acesso e chamar a Graph API do AD do Azure para acessar os dados do diretório do locatário. Por exemplo, embora o token obtido durante a entrada contenha informações do usuário, como o nome e o endereço de email, seu aplicativo pode precisar de informações, como associações a um grupo ou o nome do gerente do usuário. Essas informações podem ser obtidas do diretório do locatário usando a Graph API. Para obter mais informações sobre a Graph API, consulte [este tópico][].

Para que seu aplicativo possa chamar a AD Graph do Azure, ele deve se autenticar e obter um token de acesso. Os tokens de acesso são obtidos por meio da autenticação do aplicativo com os respectivos ID do Cliente e Segredo do Cliente. As etapas a seguir mostram como fazer para:

1.	Usar uma classe proxy gerada para chamar o AD Graph do Azure
2.	Adquirir um token de acesso usando a AAL (Biblioteca de Autenticação do Azure) 
3.	Chamar o AD Graph do Azure para obter uma lista de usuários locatários

<div class="dev-callout"><strong>Observação</strong><p>A biblioteca auxiliar do aplicativo de exemplo Microsoft.IdentityModel.WAAD.Preview já contém uma classe proxy gerada automaticamente (criada pela adição de uma referência de serviço a https://graph.windows.net/your-domain-name chamada GraphService). O aplicativo usará essa classe proxy para chamar o serviço do AD Graph do Azure.</p></div>

<h3>Etapa 1: Usar uma classe proxy para chamar o AD Graph do Azure</h3>
Nesta etapa, vamos usar o aplicativo de exemplo para mostrar como:

1.	Criar um ponto de extremidade do AD Graph do Azure específico ao locatário
2.	Usar o ponto de extremidade para criar uma instância do proxy para o Graph
3.	Adicionar o cabeçalho de autorização à solicitação e adquirir o token.  

No aplicativo de exemplo, essas chamadas para a API são manipuladas pelo método GraphInterface na classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*, conforme mostrado no código a seguir.

	public GraphInterface()
    {
    	// 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

<h3>Etapa 2: Adquirir um token de acesso usando a Biblioteca de Autenticação do Azure</h3>
O aplicativo de exemplo usa a AAL (Biblioteca de Autenticação do Azure) para adquirir tokens para acessar a Graph API.  O processo de aquisição de token é gerenciado pelo método *GetAuthorizationHeader* na classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* e é mostrado abaixo.  

<div class="dev-callout"><strong>Observação</strong><p>A AAL está disponível como um pacote NuGet e pode ser instalada no Visual Studio.</p></div>

	/// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

As informações a seguir são usadas para obter o token de acesso, conforme demonstrado no código acima:

1.	As informações do aplicativo (ClientID, ServicePrincipalKey e AppHostname)
2.	As informações do destino, que é o Graph e é referenciado como ServiceRealm acima
3.	O TenantDomainName que você adquiriu anteriormente

<h3>Etapa 3: Chamar o AD Graph do Azure para obter uma lista de usuários</h3>
O método a seguir na classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* obtém uma lista de todos os usuários de seu locatário usando o proxy *DataService* gerado anteriormente.

	public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

Esse método é chamado no arquivo **HomeController.cs** para mostrar a lista de usuários na guia Usuários no aplicativo web.

<h2><a name="publish"></a>Parte 5: Publicar seu aplicativo</h2>

Depois de testar completamente seu aplicativo, você pode criar um detalhe do aplicativo e publicá-lo no Azure Marketplace. Estas etapas são executadas no Painel do Vendedor da Microsoft.  

<div class="dev-callout"><strong>Observação</strong><p>Seu aplicativo é responsável por gerenciar qualquer relação de cobrança com seus clientes. O Azure Marketplace fornece apenas links para o site de seu aplicativo e informações sobre ele.</p></div>

<h3>Etapa 1: Criando um manifesto e o detalhe do aplicativo</h3>

Antes de criar um detalhe do aplicativo, você deve gerar uma nova ID do Cliente e Segredo do Cliente para a versão de produção do seu aplicativo. Na Parte 1 deste tutorial passo a passo, você gerou uma ID do Cliente e um Segredo do Cliente para uma versão de teste de seu aplicativo. Repita essas etapas e configure seu aplicativo para usar os novos valores, garantindo que você defina um Domínio de Aplicativo e uma URL de Redirecionamento de Aplicativo de produção.

Em seguida, você deve criar um manifesto de aplicativo que liste as permissões que o aplicativo irá solicitar para o consentimento do cliente. Esse manifesto é escrito em formato XML regido por um arquivo XSD. O manifesto deve ser carregado como parte do detalhe do aplicativo que você está criando. 

Há três diferentes níveis de permissão, conforme descrito na Parte 1 deste tutorial passo a passo:

**DirectoryReader**: concede permissão para ler dados do diretório, como contas de usuário, grupos e informações sobre sua organização. Habilita SSO.

**UserAccountAdministrator**: concede permissão para ler e gravar dados, como usuários, grupos e informações sobre sua organização. Habilita SSO.

**None**: permite logon único mas desabilita o acesso aos dados do diretório.

Dois exemplos de manifesto de aplicativo são incluídos abaixo. O primeiro demonstra as permissões para um aplicativo de somente SSO e o segundo demonstra as permissões para um aplicativo de somente leitura:

	<?xml version="1.0" encoding="utf-16"?>
	<AppRequiredPermissions>
  	  <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


	<?xml version="1.0" encoding="utf-16"?>
	<AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="en-us" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

O atributo *Policy* nos exemplos acima descreve o tipo de permissão de aplicativo que está sendo solicitado. Atualmente, há suporte apenas para o atributo de permissão "AppOnly". Esse tipo de permissão indica que o aplicativo acessa apenas o próprio diretório. 

O elemento *Reason* opcional permite especificar sua justificativa (em várias culturas) para o nível de permissão necessário. Esse texto é exibido na página de consentimento para ajudar o cliente ao aprovar ou rejeitar seu aplicativo.

Usando sua nova ID do Cliente e o manifesto do aplicativo, você pode criar um detalhe do aplicativo seguindo as instruções em [Adicionar aplicativos no Painel do Vendedor da Microsoft][]. Ao criar um detalhe do aplicativo, certifique-se de selecionar o tipo de aplicativo AD do Azure. Ao concluir seu detalhe do aplicativo, clique em "Enviar" para publicar o aplicativo no Azure Marketplace. Você precisará aguardar até que o aplicativo seja aprovado para que a publicação seja concluída.

<div class="dev-callout"><strong>Observação</strong><p>Se você receber uma solicitação para "adicionar informações de imposto e pagamento", poderá ignorar essa etapa porque você está vendendo seu aplicativo diretamente ao cliente e não por meio da Microsoft.</p></div>

<h3>Etapa 2: Finalizar o teste e tornar seu aplicativo público</h3>
Depois que o detalhe de seu aplicativo for aprovado, você deverá testar completamente o aplicativo mais uma vez. Por exemplo, verifique se seu aplicativo foi atualizado com a ID do Cliente e o Segredo do Cliente de produção.  Percorra a lista de verificação de teste uma última vez, garantindo que a página de consentimento agora mostra as informações incluídas no detalhe de seu aplicativo.

<h2><a name="summary"></a>Resumo</h2>
Neste tutorial passo a passo, você aprendeu como integrar seu aplicativo de multilocação com o AD do Azure. O processo incluiu três etapas:

* Habilitar os clientes para entrarem em seu aplicativo usando o AD do Azure
* Habilitar SSO (logon único) com o AD do Azure
* Consultar dados de um diretório do cliente usando a Graph API do AD do Azure

A integração com o AD do Azure permite que seus clientes inscrevam-se e entrem em seu aplicativo usando um sistema de gerenciamento de identidades que ele já mantém, o que reduz ou elimina a necessidade de separar as tarefas de gerenciamento de identidades com seu aplicativo. Essa funcionalidade dá a seus clientes uma experiência mais transparente ao usar seu aplicativo e libera o tempo gasto com tarefas de gerenciamento.


[Introdução]: #introduction
[Parte 1: Obter uma ID de cliente para acesso ao AD do Azure]: #getclientid
[Parte 2: Habilitar os clientes para entrarem usando o AD do Azure]: #enablesignup
[Parte 3: Habilitar Logon Único]: #enablesso
[Parte 4: Acessar o AD Graph do Azure]: #accessgraph
[Parte 5: Publicar seu aplicativo]: #publish
[Resumo]: #summary
[Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
[Pacote NuGet do AAL x86]: http://g.microsoftonline.com/1AX00en/124
[Pacote NuGet do AAL x64]: http://g.microsoftonline.com/1AX00en/125
[Ferramenta de identidade e acesso do Visual Studio]: http://g.microsoftonline.com/1AX00en/126
[Windows Identity Foundation 3.5]: http://g.microsoftonline.com/1AX00en/127
[WCF Data Services para OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
[Página Identidade]: http://www.windowsazure.com/pt-br/home/features/identity/

[baixado aqui]: http://go.microsoft.com/fwlink/?LinkId=271213
[atribuição da porta no Visual Studio]: http://msdn.microsoft.com/pt-br/library/ms178109(v=vs.100).aspx
[Painel do vendedor da Microsoft]: https://sellerdashboard.microsoft.com/
[criar um perfil de conta]: http://msdn.microsoft.com/pt-br/library/jj552460.aspx
[Criar Ids e Segredos de clientes no Painel do Vendedor da Microsoft]: http://msdn.microsoft.com/pt-br/library/jj552461.aspx
[Obter um locatário do AD do Azure]: http://g.microsoftonline.com/0AX00en/5
[este tópico]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh974476.aspx
[Adicionar aplicativos no Painel do Vendedor da Microsoft]: http://msdn.microsoft.com/pt-br/library/jj552465.aspx
[login]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png

