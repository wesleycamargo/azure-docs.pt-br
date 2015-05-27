<properties 
	pageTitle="Depurar um aplicativo de API no Serviço de Aplicativo do Azure" 
	description="Saiba como depurar um aplicativo de API enquanto ele é executado no Serviço de Aplicativo do Azure, usando o Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Depurar um aplicativo de API no Serviço de Aplicativo do Azure 

## Visão geral

Neste tutorial, você vai depurar o código de API Web ASP.NET enquanto ele é executado um [aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](app-service-value-prop-what-is.md). O tutorial funciona com o aplicativo de API que você [criar](app-service-dotnet-create-api-app.md) e [implantar](app-service-dotnet-deploy-api-app.md) nos tutoriais anteriores desta série.

Comece usando o recurso **Cliente do aplicativo de API** para gerar o código do cliente que chama o aplicativo de API implantado. Em seguida, você vai depurar o aplicativo cliente e o aplicativo de API simultaneamente, com o aplicativo de API em execução na nuvem.

## Gerar um cliente de aplicativo de API 

As ferramentas do aplicativo de API no Visual Studio facilitam a geração do código em C# que chama os aplicativos de API do Azure por meio da área de trabalho, armazenamento e aplicativos móveis.

1. No Visual Studio, abra a solução que contém o aplicativo de API do tópico [Criar aplicativo de API](app-service-dotnet-create-api-app.md). 

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e selecione o **Adicionar** > **Novo Projeto**.

	![Adicionar um novo projeto](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. Na caixa de diálogo **Adicionar Novo Projeto**, execute as seguintes etapas:

	1. Selecione a categoria **Área de Trabalho do Windows**.
	
	2. Selecione o modelo do projeto **Aplicativo de Console**.
	
	3. Nomeie o projeto.
	
	4. Clique em **OK** para gerar o novo projeto na solução existente.
	
	![Adicionar um novo projeto](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. Clique com o botão direito do mouse no projeto de aplicativo de console recém-criado e selecione **Adicionar** > **Cliente de aplicativo de API do Azure**.

	![Adicionar um novo cliente](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. Na caixa de diálogo **Adicionar Cliente de Aplicativo de API do Microsoft Azure**, execute as seguintes etapas:

	1. Selecione a opção **Baixar**. 
	
	2. Na lista suspensa, selecione o aplicativo de API que você criou anteriormente.
	
	3. Clique em **OK**.

	![Tela de geração](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	O assistente vai baixar o arquivo de metadados de API e gerar uma interface digitada para invocar o aplicativo de API.

	![Geração acontecendo](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	Após a conclusão da geração de código, você verá uma nova pasta no Gerenciador de Soluções, com o nome do aplicativo de API. Esta pasta contém o código que implementa os cliente e modelos de dados.

	![Geração completa](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. Abra o arquivo **Program.cs** na raiz do projeto e substitua o método **Principal** pelo código a seguir:

		static void Main(string[] args)
	    {
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
	    }

## Testar o cliente do aplicativo de API

Depois que o aplicativo de API tiver sido codificado, é hora de testar o código no navegador.

1. Abra o **Gerenciador de Soluções**.

2. Clique com o botão direito do mouse no aplicativo de console que você criou na seção anterior.

3. No menu de contexto do aplicativo de console, selecione **Depurar > Iniciar nova instância**.

4. Uma janela de console deve abrir e exibir todos os contatos.

	![Aplicativo de console em execução](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. Pressione **Enter** para descartar a janela de console.

## Depurar o aplicativo de API 

Agora que o cliente e o aplicativo de API estão codificados e testados, vamos ver como depurá-lo.

1. No menu **Exibir** do Visual Studio, selecione **Gerenciador de Servidores**. 

2. No **Gerenciador de Servidores**, expanda o nó **Azure > Serviço de Aplicativo**.

3. Localize o grupo de recursos que você criou quando implantou o aplicativo de API.

4. No grupo de recursos, clique com o botão direito no nó do aplicativo de API e selecione **Anexar Depurador**.

	![Anexando o depurador](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	O depurador remoto tentará se conectar. Em alguns casos, talvez seja necessário clicar novamente em **Anexar Depurador** para estabelecer uma conexão. Portanto, em caso de falha, tente novamente.

	![Anexando o depurador](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. Depois que a conexão for estabelecida, abra o arquivo **ContactsController.cs** no aplicativo de API e adicione pontos de interrupção em `Get` e `Post` métodos. A princípio, podem não aparecer como ativos, mas se o depurador remoto estiver anexado, você estará pronto para depurar.

	![Aplicando pontos de interrupção ao controlador](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. Para depurar, clique com o botão direito no aplicativo de console no **Gerenciador de Soluções** e selecione **Depurar** > **Iniciar nova instância**. Agora você pode depurar remotamente o aplicativo de API e localmente o aplicativo cliente e ver todo o fluxo de dados.

	A captura de tela a seguir mostra o depurador quando ele atinge o ponto de interrupção do `Post` método. Você pode ver que os dados de contato do cliente foram desserializados em um objeto `Contact` fortemente tipado.

	![Depurando o cliente local para alcançar a API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Próximas etapas

Depuração remota para aplicativos de API torna mais fácil ver como o código está em execução no Serviço de Aplicativo do Azure. Diagnóstico avançado e depuração de dados estão disponíveis no IDE do Visual Studio para aplicativos de API do Azure.

Aplicativos de API do Serviço de Aplicativo são aplicativos Web do Serviço de Aplicativo com recursos adicionais para hospedagem de serviços Web, para que você possa usar as mesmas ferramentas de depuração e solução de problemas para aplicativos de API que você usa em aplicativos Web. Para obter mais informações, consulte [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

O aplicativo de API que você criou nesta série está disponível publicamente para qualquer pessoa que chamar. Para obter informações sobre como proteger o aplicativo de API para que somente usuários autenticados possam chamá-lo, consulte [Proteger um aplicativo de API: adicionar autenticação do Active Directory do Azure ou do provedor social](app-service-api-dotnet-add-authentication.md).

<!--HONumber=54-->