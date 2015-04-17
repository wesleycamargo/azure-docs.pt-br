<properties 
	pageTitle="Depurar remotamente um aplicativo de API do Serviço de Aplicativo do Azure" 
	description="Usando o Visual Studio para depurar remotamente um aplicativo de API do Serviço de Aplicativo do Azure." 
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

# Depurar remotamente um aplicativo de API do Serviço de Aplicativo do Azure 

## Visão geral

Os recursos de depuração remota do Visual Studio foram ampliados para incluir suporte para aplicativos de API no Serviço de Aplicativo do Azure.  Isso significa que você pode usar ferramentas de depuração conhecidas para ver seu código em execução no Azure.  Este tópico demonstra como usar o **Cliente de Aplicativo de API** do Visual Studio para gerar o código do cliente que chama o aplicativo de API implantado.  Em seguida, você vai depurar o aplicativo cliente e o aplicativo de API simultaneamente, com o aplicativo de API em execução na nuvem.

Este tutorial é o último de uma série de três:

1. Em [Criar um Aplicativo de API](app-service-dotnet-create-api-app.md), você criou um projeto de Aplicativo de API. 
* Em [Implantar um Aplicativo de API](app-service-dotnet-deploy-api-app.md), você implantou o aplicativo de API em sua assinatura do Azure.
* Neste tutorial, você usa o Visual Studio para depurar remotamente o código enquanto ele é executado no Azure.

## Gerar um cliente de aplicativo de API 

As ferramentas de aplicativo de API no Visual Studio facilitam o processo de gerar código em C#, que chama seus aplicativos de API do Azure da área de trabalho, do repositório e de aplicativos móveis. 

No Visual Studio, abra a solução que contém o aplicativo de API do [primeiro](app-service-dotnet-create-api-app.md) tutorial.  Clique com o botão direito do mouse na solução e selecione **Adicionar** > **Novo Projeto**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Selecione a categoria **Área de Trabalho do Windows** e o modelo de projeto **Aplicativo de Console**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Clique com o botão direito do mouse no projeto de aplicativo de console e selecione **Adicionar** > **Cliente de Aplicativo de API do Azure**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
Na caixa de diálogo, selecione a opção **Baixar**.  Na lista suspensa, selecione o aplicativo de API que você criou anteriormente.  Clique em **OK**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

O assistente vai baixar o arquivo de metadados da API e gerar uma interface tipada para invocar o aplicativo de API.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

Após a conclusão da geração do código, você verá uma nova pasta no Gerenciador de Soluções, com o nome do aplicativo de API.  Esta pasta contém o código que implementa os modelos de dados e clientes. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Abra o arquivo **Program.cs** na raiz do projeto e substitua o método **Principal** pelo código a seguir: 

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

No menu **Exibição**, selecione **Gerenciador de Servidores**.  Na janela Gerenciador de Servidores, expanda o nó *Azure > Serviço de Aplicativo**.  Localize o grupo de recursos que você criou quando implantou o aplicativo de API.  Clique com o botão direito no nó do seu aplicativo de API e selecione **Anexar Depurador**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

O depurador remoto tentará se conectar.  Em alguns casos, talvez seja necessário clicar novamente em **Anexar Depurador** para estabelecer uma conexão. Portanto, em caso de falha, tente novamente.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

Depois que a conexão é estabelecida, abra o arquivo **ContactsController.cs** no aplicativo de API e adicione pontos de interrupção aos métodos `Get` e `Post`.  Eles podem não aparecer como ativos primeiro, mas se o depurador remoto estiver anexado, você está pronto para depurar. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Para depurar, clique com o botão direito no aplicativo de console no Gerenciador de Soluções e selecione **Depurar** > **Iniciar nova instância**.  Agora você pode depurar remotamente o aplicativo de API e localmente o aplicativo localmente e ver todo o fluxo de dados. 

A captura de tela a seguir mostra o depurador quando ele atinge o ponto de interrupção para o método `Post`.  Você pode ver que os dados de contato do cliente foi desserializados em um objeto `Contact` fortemente tipado. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Resumo

A depuração remota dos aplicativos da API torna mais fácil ver como o código está sendo executado no Serviço de Aplicativo do Azure.  Dados avançados de diagnóstico e de depuração estão disponíveis no IDE do Visual Studio para seus aplicativos de API do Azure em execução remota.


<!--HONumber=49-->