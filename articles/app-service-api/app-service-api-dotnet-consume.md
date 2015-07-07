<properties 
	pageTitle="Consumir um aplicativo de API no serviço de aplicativo do Azure por meio de um cliente .NET" 
	description="Saiba como consumir um aplicativo de API a partir de um cliente .NET usando o SDK do serviço de aplicativo." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="tdykstra"/>

# Consumir um aplicativo de API no serviço de aplicativo do Azure por meio de um cliente .NET 

## Visão geral

Este tutorial mostra como usar o SDK do serviço de aplicativo para escrever um código que chama um [aplicativo de API](app-service-api-apps-why-best-platform.md) que foi configurado para nível de acesso **Público (anônimo)** ou **Público (autenticado)**. O artigo aborda os seguintes cenários de exemplo:

- Chamar um aplicativo de API **Público (anônimo)** de um aplicativo de console
- Chamar um aplicativo de API **Público (autenticado)** de um aplicativo de área de trabalho do Windows 

Cada uma dessas seções do tutorial é independente - você pode seguir as instruções para o segundo cenário sem ter concluído as etapas para primeiro.

Para obter informações sobre como chamar um aplicativo de API **Interno**, consulte [Consumir um aplicativo de API interno de um cliente .NET](app-service-api-dotnet-consume-internal.md).

## Pré-requisitos

o tutorial presume que você esteja familiarizado com a criação de projetos e com adicionar código a eles no Visual Studio, e como [Gerenciar aplicativos de API no portal de visualização do Azure](app-service-api-apps-manage-in-portal.md).

Os exemplos de projeto e o código neste artigo baseiam-se no projeto de aplicativo de API que você criar, implantar e proteger nesses artigos:

- [Criar um aplicativo de API](app-service-dotnet-create-api-app.md)
- [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md)
- [Proteger um aplicativo de API](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requer a versão 2.6 ou posterior do SDK do Azure para .NET.

## Chamada não autenticada de um aplicativo de console

Nesta seção você cria um projeto de aplicativo de console e adiciona a ele um código, que por sua vez chama um aplicativo de API que não requer autenticação.

### Configurar o aplicativo de API e criar o projeto

1. Se você ainda não o fez, siga a [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md) para implantar o projeto de exemplo ContactsList em um aplicativo de API na sua assinatura do Azure.

	Esse tutorial direciona você para definir o nível de acesso na caixa de diálogo publicar do Visual Studio como **Disponível para qualquer pessoa**, que é o mesmo que **Público (anônimo)** no portal. No entanto, se você fez o tutorial [Proteger um aplicativo API](../app-service-dotnet-add-authentication.md) depois disso, o nível de acesso foi definido como **Público (autenticado)** e, nesse caso, você precisa alterá-lo conforme indicado na etapa a seguir.

2. No [Portal de Visualização do Azure](https://portal.azure.com/), na folha **Aplicativo de API** para o aplicativo de API que você deseja chamar, vá para **Configurações > Configurações do aplicativo** e defina **Nível de acesso** como **Público (anônimo)**.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. No Visual Studio, crie um projeto de aplicativo de console.
 
### Adicionar código de cliente gerado pelo SDK do serviço de aplicativo

3. Em **Gerenciador de soluções**, clique com o botão direito no projeto (não na solução) e selecione **Adicionar > Cliente de aplicativo de API do Azure**. 

	![](./media/app-service-api-dotnet-consume/03-add-azure-api-client-v3.png)
	
3. Na caixa de diálogo **Adicionar cliente de aplicativo de API do Azure**, clique em **Baixar a partir do aplicativo de API do Azure**.

5. Na lista suspensa, selecione o aplicativo de API que você deseja chamar.

7. Clique em **OK**.

	![Tela de geração](./media/app-service-api-dotnet-consume/04-select-the-api-v3.png)

	O assistente baixa o arquivo de metadados de API e gera uma interface digitada para chamar o aplicativo de API.

	![Geração acontecendo](./media/app-service-api-dotnet-consume/05-metadata-downloading-v3.png)

	Após a conclusão da geração de código, você vê uma nova pasta **Gerenciador de soluções**, com o nome do aplicativo de API. Esta pasta contém o código que implementa os modelos de dados e classes de cliente.

	![Geração completa](./media/app-service-api-dotnet-consume/06-code-gen-output-v3.png)

### Adicione código para chamar o aplicativo de API

Para chamar o aplicativo de API, tudo o que você precisa fazer é criar um objeto de cliente e chamar métodos nele, como neste exemplo:

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Abra *Program.cs* e adicione o seguinte código no método `Main`.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Pressione CTRL+F5 para executar o aplicativo.

	![Geração completa](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Chamada autenticada a partir de um aplicativo de área de trabalho do Windows

Nesta seção você cria um projeto de aplicativo de desktop do Windows e adiciona a ele um código, que por sua vez chama um aplicativo de API que requer autenticação. Esse código implementa o *fluxo de autenticação de servidor* Oauth 2, que significa que o gateway de aplicativo de API, em vez do aplicativo cliente, recebe o token do provedor de autenticação.

Aplicativos de API do Azure também dão suporte ao fluxo de autenticação de cliente. Um cenário de autenticação de fluxo do cliente será adicionado a este tutorial no futuro.

### Configurar o aplicativo de API e criar o projeto

1. Execute o tutorial [Proteger um aplicativo API](../app-service-dotnet-add-authentication.md) para configurar um aplicativo de API com nível de acesso **Público (autenticado)**.

1. No Visual Studio, crie um projeto para desktop do Windows Forms.

2. No designer de formulário, adicione os seguintes controles:

	* Um controle de botão
	* Um controle de caixa de texto
	* Um controle de navegador da Web

3. Defina o controle de caixa de texto como de várias linhas.

	Seu formulário deverá parecer com o exemplo a seguir.

	![](./media/app-service-api-dotnet-consume/form.png)

### Adicionar código de cliente gerado pelo SDK do serviço de aplicativo

3. Em **Gerenciador de soluções**, clique com o botão direito no projeto (não na solução) e selecione **Adicionar > Cliente de aplicativo de API do Azure**. 

3. Na caixa de diálogo **Adicionar cliente de aplicativo de API do Azure**, clique em **Baixar a partir do aplicativo de API do Azure**.

5. Na lista suspensa, selecione o aplicativo de API que você deseja chamar, então clique em **OK**.

### Adicione código para chamar o aplicativo de API

5. No Portal de Visualização do Azure, copie a URL para o gateway do seu aplicativo de API. Você usará esse valor na próxima etapa.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. No código-fonte *Form1.CS*, adicione o seguinte código antes do construtor `Form1()`, substituindo o valor de GATEWAY_URL pelo valor que você copiou na etapa anterior. Certifique-se de incluir a barra à direita (/).

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. No designer de formulário, clique duas vezes no botão para adicionar um manipulador de click e, em seguida, no método adicionar código do manipulador para ir para a URL de logon para o gateway, por exemplo:

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Substitua "[authprovider]" pelo código para o provedor de serviço de identidade que você configurou no gateway, por exemplo, "aad", "twitter", "google", "microsoftaccount" ou "facebook". Por exemplo:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Adicione um manipulador de eventos `DocumentCompleted` ao controle do navegador da Web e adicione o seguinte código ao método do manipulador.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	O código que você adicionou é executado depois que o usuário fizer logon usando o controle de navegador da Web. Após um logon bem-sucedido, a URL de resposta contém a ID de usuário e senha. O código extrai esses valores da URL, fornece-os para o objeto de cliente de serviço do aplicativo e então utiliza tal objeto para criar um objeto de cliente de aplicativo de API. Você pode chamar a API chamando métodos nesse objeto cliente de aplicativo de API.

8. Pressione CTRL+F5 para executar o aplicativo.

9. Clique no botão e, quando o controle de navegador exibir uma página de logon, insira as credenciais de um usuário autorizado a chamar o aplicativo de API.

	O Azure autentica você, o aplicativo chama o aplicativo de API e exibe a resposta.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

## Próximas etapas

Este artigo mostra como utilizar um aplicativo de API de um cliente .NET, para aplicativos de API definidos com os níveis de acesso **Público (autenticado)** e **Público (anônimo)**.

Para obter exemplos adicionais de um código que chame um aplicativo de API de clientes .NET, baixe o aplicativo de exemplo [Azure cartões](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).
 

<!---HONumber=62-->