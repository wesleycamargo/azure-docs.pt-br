<properties 
	pageTitle="Conectando-se a conta dos serviços de mídia usando o .NET" 
	description="Este tópico demonstra como se conectar os serviços de mídia usando o .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


# Conectando-se a conta dos serviços de mídia usando o SDK dos serviços de mídia para .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect_programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


Este tópico descreve como obter uma conexão programática aos serviços de mídia do Microsoft Azure quando você estiver programando com o SDK dos serviços de mídia para .NET.


## Conectando aos Serviços de Mídia

Para se conectar aos serviços de mídia por meio de programação, você deve ter configurado anteriormente uma conta do Azure, configurado os serviços de mídia nessa conta e configurado um projeto do Visual Studio para desenvolvimento com o SDK dos serviços de mídia para .NET. Para obter mais informações, consulte a instalação para desenvolvimento com o SDK dos serviços de mídia para .NET.

No final do processo de instalação da conta dos serviços de mídia, você obteve os seguintes valores de conexão necessárias. Use-os para fazer conexões programáticas aos serviços de mídia.

- O nome da conta de serviços de mídia.

- A chave da conta de serviços de mídia.

Para localizar esses valores, vá para o Portal de Gerenciamento do Azure, selecione sua conta de serviço de mídia e clique no ícone "**GERENCIAR CHAVES**" na parte inferior da janela do portal. Clicando no ícone ao lado de cada caixa de texto, o valor é copiado para a área de transferência do sistema.


## Criando uma instância CloudMediaContext

Para começar a programar em relação aos serviços de mídia, você precisa criar uma instância **CloudMediaContext** que representa o contexto do servidor. O **CloudMediaContext** inclui referências para coleções importantes incluindo trabalhos, ativos, arquivos, políticas de acesso e localizadores.

>[AZURE.NOTE]A classe **CloudMediaContext** não é thread-safe. Você deve criar um novo CloudMediaContext por thread ou por conjunto de operações.


O CloudMediaContext possui cinco sobrecargas de construtor. É recomendável usar construtores que usam **MediaServicesCredentials** como um parâmetro. Para obter mais informações, consulte **Reutilizando Tokens de serviço de controle de acesso**, a seguir.

O exemplo a seguir usa o construtor CloudMediaContext(credenciais MediaServicesCredentials) público:

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Reutilizando Tokens do serviço de controle de acesso

Esta seção mostra como reutilizar tokens do serviço de controle de acesso usando os construtores de CloudMediaContext que usam MediaServicesCredentials como um parâmetro.


O [Controle de acesso do Active Directory do Azure](https://msdn.microsoft.com/library/hh147631.aspx) (também conhecido como Serviço de controle de acesso ou ACS) é um serviço baseado em nuvem que fornece uma maneira fácil de autenticar e autorizar os usuários para ganhar acesso a seus aplicativos Web. Os serviços de mídia do Microsoft Azure controla o acesso a seus serviços embora o protocolo OAuth que requer um token ACS. Os serviços de mídia recebem os tokens ACS de um servidor de autorização.

Ao desenvolver com o SDK dos serviços de mídia, você pode optar por não lidar com tokens, porque o código do SDK o gerencia pra você. No entanto, permitir que o SDK gerencie completamente os tokens ACS leva a solicitações de token desnecessárias. Solicitar tokens leva tempo e consome os recursos de cliente e servidor. Além disso, o servidor ACS acelera as solicitações se a taxa for muito alta. O limite é de 30 solicitações por segundo. Consulte [Limitações do Serviço ACS](https://msdn.microsoft.com/library/gg185909.aspx) para obter mais detalhes.

Começando com o SDK dos serviços de mídia na versão 3.0.0.0, é possível reutilizar os tokens ACS. Os construtores do **CloudMediaContext** que usam **MediaServicesCredentials** como um parâmetro habilitam o compartilhamento de tokens ACS entre vários contextos. A classe MediaServicesCredentials encapsula as credenciais de serviços de mídia. Se um token ACS está disponível e seu tempo de expiração é conhecido, você pode criar uma nova instância de MediaServicesCredentials com o token e passá-la para o construtor do CloudMediaContext. Observe que o SDK dos serviços de mídia atualiza automaticamente os tokens assim que eles expiram. Há duas maneiras de reutilizar os tokens ACS, conforme mostrado nos exemplos a seguir.

- Você pode armazenar em cache o objeto do **MediaServicesCredentials** na memória (por exemplo, em uma variável de classe estática). Em seguida, passe o objeto armazenado em cache para o construtor CloudMediaContext. O objeto MediaServicesCredentials contém um token de ACS que pode ser reutilizado se ainda estiver válido. Se o token não é válido, ele será atualizado pelo SDK dos serviços de mídia usando as credenciais fornecidas ao construtor MediaServicesCredentials.

	Observe que o objeto **MediaServicesCredentials** obtém um token válido depois que o RefreshToken é chamado. O **CloudMediaContext** chama o método **RefreshToken** no construtor. Se você estiver planejando salvar os valores do token para um armazenamento externo, certifique-se de verificar se o valor de TokenExpiration é válido antes de salvar os dados do token. Se não for válido, chame RefreshToken antes de armazenar em cache.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Você pode também armazenar em cache a cadeia de caracteres AccessToken e os valores de TokenExpiration. Os valores podem ser usados depois para criar um novo objeto MediaServicesCredentials com os dados em token armazenados em cache. Isso é especialmente útil para cenários em que o token pode ser compartilhado com segurança entre vários processos ou computadores.

	Os trechos de código a seguir chamam os métodos SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage e UpdateTokenDataInExternalStorageIfNeeded que não são definidos neste exemplo. É possível definir esses métodos para armazenar, recuperar e atualizar dados do token no armazenamento externo.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Use os valores do token salvos para criar MediaServicesCredentials.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Atualize a cópia do token caso o token tenha sido atualizado pelo SDK dos Serviços de Mídia.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- Se você tiver várias contas de serviços de mídia (por exemplo, para fins ou distribuição geográfica de compartilhamento de carga) você pode armazenar em cache objetos MediaServicesCredentials usando a coleção de System.Collections.Concurrent.ConcurrentDictionary (a coleção de ConcurrentDictionary representa uma coleção thread-safe de pares de chave/valor que pode ser acessada simultaneamente por vários threads). Em seguida, você pode usar o método GetOrAdd para obter as credenciais armazenadas em cache.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Conectando-se a uma conta de serviços de mídia localizada na região Norte da China

Se sua conta estiver localizada na região Norte da China, use o seguinte construtor:

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Por exemplo:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Armazenando valores de conexão na configuração

É uma prática altamente recomendável armazenar valores de conexão, especialmente valores confidenciais como seu nome de conta e senha, na configuração. Além disso, é uma prática recomendada criptografar dados de configuração confidenciais. Você pode criptografar o arquivo de configuração inteiro usando o sistema de arquivos com criptografia (EFS) do Windows. Para habilitar o EFS em um arquivo, clique com o botão direito do mouse no arquivo, selecione **Propriedades** e habilite a criptografia na guia de configurações **Avançadas**. Ou você pode criar uma solução personalizada para criptografar as partes selecionadas de um arquivo de configuração usando a configuração protegida. Consulte [Criptografando informações de configuração usando configuração protegida](https://msdn.microsoft.com/library/53tyfkaw.aspx).

O arquivo App.config a seguir contém os valores de conexão necessárias. Os valores no elemento <appSettings> são os valores necessários que você obteve do processo de configuração de conta dos serviços de mídia.

	<configuration>
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	</configuration>


Para recuperar valores de conexão da configuração, você pode usar a classe **ConfigurationManager** e, em seguida, atribuir valores aos campos em seu código:
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)



<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=Oct15_HO3-->