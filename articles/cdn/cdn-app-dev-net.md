<properties
	pageTitle="Introdução à biblioteca do Azure CDN para .NET | Microsoft Azure"
	description="Aprenda a gravar aplicativos .NET para gerenciar o Azure CDN usando o Visual Studio."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# Introdução à biblioteca do Azure CDN para .NET

Você pode usar a [Biblioteca do Azure CDN para .NET](https://msdn.microsoft.com/library/mt657769.aspx) para automatizar a criação e o gerenciamento de perfis CDN e de pontos de extremidade. Este tutorial o orientará na criação de um aplicativo de console simples do .NET, que demonstra várias das operações disponíveis. Este tutorial não pretende descrever todos os aspectos da biblioteca do Azure CDN para o .NET em detalhes.

Você precisará do Visual Studio 2015 para concluir este tutorial. O [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está disponível gratuitamente para download.

Um exemplo completo deste tutorial pode ser encontrado [aqui](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

[AZURE.INCLUDE [aplicativo de desenvolvimento de preparação para CDN](../../includes/cdn-app-dev-prep.md)]

## Crie seu projeto e adicione pacotes NuGet

Agora que criamos um grupo de recursos para nossos perfis CDN e demos permissão para o nosso aplicativo Azure AD gerenciar os perfis CDN e os pontos de extremidade neste grupo, podemos começar a criação do nosso aplicativo.

Do Visual Studio 2015, clique em **Arquivo**, **Novo**, **Projeto...** para abrir a caixa de diálogo do novo projeto. Expanda **Visual C#** e, em seguida, selecione **Windows** no painel à esquerda. Clique em **Aplicativo de Console** no painel central. Nomeie o projeto e clique em **OK**.

![Novo Projeto](./media/cdn-app-dev-net/cdn-new-project.png)

Nosso projeto usará algumas bibliotecas do Azure contidas em pacotes NuGet. Vamos adicioná-las ao projeto.

1. Clique no menu **Ferramentas**, em **Gerenciador de Pacotes Nuget** e em **Console do Gerenciador de Pacotes**.

	![Gerenciar pacotes NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. No Console do Gerenciador de Pacotes, execute o seguinte comando para instalar a **ADAL (Biblioteca de Autenticação do Active Directory)**:

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Execute o seguinte para instalar a **biblioteca de gerenciamento do Azure CDN**:

	`Install-Package Microsoft.Azure.Management.Cdn`

## Diretivas, constantes, método principal e métodos auxiliares

Vejamos a estrutura básica do nosso programa gravado.

1. De volta à guia Program.cs, substitua as diretivas `using` na parte superior com o seguinte:

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. Precisamos definir algumas constantes que serão usadas nos nossos métodos. Na classe `Program`, mas antes do método `Main`, adicione o seguinte. Certifique-se de substituir os espaços reservados, incluindo os **&lt;colchetes angulares&gt;**, com seus próprios valores conforme necessário.

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. Também no nível de classe, defina essas duas variáveis. Nós as usaremos posteriormente para determinar se o perfil e o ponto de extremidade já existem.

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  Substitua o método `Main` da seguinte maneira:

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. Alguns dos nossos outros métodos solicitarão que o usuário responda perguntas do tipo "Sim/Não". Adicione o seguinte método para tornar esse processo um pouco mais fácil:

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

Agora que a estrutura básica do nosso programa está gravada, devemos criar métodos chamados pelo método `Main`.

## Autenticação

Para que possamos usar a biblioteca de gerenciamento do Azure CDN, é necessário autenticar nossa entidade de serviço e obter um token de autenticação. Esse método usa a ADAL para recuperar o token.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

Se você estiver usando a autenticação de usuário individual, o método `GetAccessToken` terá uma aparência ligeiramente diferente.

>[AZURE.IMPORTANT] Use este exemplo de código somente se optar pela autenticação de usuário individual, em vez de uma entidade de serviço.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

Certifique-se de substituir `<redirect URI>` com o URI de redirecionamento que você inseriu quando registrou o aplicativo no Azure AD.

## Relacione os perfis CDN e os pontos de extremidade

Agora estamos prontos para executar as operações de CDN. A primeira tarefa que nosso método faz é relacionar todos os perfis e pontos de extremidade em nosso grupo de recursos e, se ele encontrar uma correspondência para os nomes de perfis e de pontos de extremidade especificados em nossas constantes, ele fará uma observação a respeito posteriormente. Portanto, não tentaremos criar duplicatas.

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## Criar perfis CDN e pontos de extremidade

Em seguida, criaremos um perfil.

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

Depois que o perfil for criado, criaremos um ponto de extremidade.

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] O exemplo acima atribui ao ponto de extremidade uma origem denominada *Contoso*, com um nome de host `www.contoso.com`. Você deve alterá-la para apontar para o nome de host de sua própria origem.

## Limpar um ponto de extremidade

Supondo que o ponto de extremidade tenha sido criado, uma tarefa comum que talvez desejemos executar em nosso programa está limpando o conteúdo no ponto de extremidade.

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] No exemplo acima, a cadeia de caracteres `/*` indica que eu quero limpar tudo na raiz do caminho do ponto de extremidade. Isso é equivalente a marcar **Limpar Tudo** na caixa de diálogo "limpeza" do Portal do Azure. No método `CreateCdnProfile`, criei nosso perfil como um perfil **Azure CDN da Verizon** usando o código `Sku = new Sku(SkuName.StandardVerizon)`; assim, ele será bem-sucedido. No entanto, o perfil **Azure CDN do Akamai** não dá suporte para a ação **Limpar Tudo**. Portanto, se eu estivesse usando um perfil do Akamai neste tutorial, eu precisaria incluir caminhos específicos para limpar.

## Excluir perfis CDN e pontos de extremidade

Os últimos métodos que incluiremos excluem nosso ponto de extremidade e perfil.

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## Executando o programa

Agora podemos compilar e executar o programa clicando no botão **Iniciar** no Visual Studio.

![Programa em execução](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando o programa atingir o prompt acima, você poderá retornar ao seu grupo de recursos no Portal do Azure e ver que o perfil foi criado.

![Sucesso!](./media/cdn-app-dev-net/cdn-success.png)

Em seguida, podemos confirmar as solicitações para executar o restante do programa.

![Programa em conclusão](./media/cdn-app-dev-net/cdn-program-running-2.png)

## Próximas etapas

Para ver o projeto concluído desse passo a passo, [baixe a amostra](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Para localizar documentação adicional sobre a biblioteca de gerenciamento do Azure CDN para .NET, confira a [referência no MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

<!---HONumber=AcomDC_0629_2016-->