<properties 
	pageTitle="Introdução aos dados (Windows Universal) | Centro de desenvolvimento de Serviços Móveis" 
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo Windows universal." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Windows universal. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx).

Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo Windows universal que armazena dados na memória, cria um novo serviço móvel, integra o serviço mível ao aplicativo e entra no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

>[AZURE.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 Update 3 para se conectar a um novo serviço móvel para um novo aplicativo Windows universal. As mesmas etapas podem ser usadas para conectar um serviço móvel a um aplicativo para Windows Store ou Windows Phone Store 8.1. Para conectar um serviço móvel a um aplicativo para Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte [Introdução aos dados para Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data).

> Se você não pode atualizar para o Visual Studio Professional 2013 Update 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data) do tópico.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo da Windows Store][Obter o aplicativo da Windows Store] 
2. [Criar o serviço móvel no Visual Studio]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar o serviço móvel]
5. [Testar o aplicativo com os Serviços Móveis]
6. [Exibir os dados carregados no Portal de Gerenciamento do Azure]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 para Windows</a> (Update 2 ou versão posterior). 


##<a name="download-app"></a>Baixar o projeto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Criar um novo serviço móvel por meio do Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>No Gerenciador de Soluções, abra o arquivo de código App.xaml.cs na pasta de projeto GetStartedWithData.Shared e avise sobre o novo campo estático que foi adicionado à classe <strong>Aplicativo</strong> dentro do bloco de compilação condicional do aplicativo Windows Store, que se parece com o exemplo a seguir:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma instância da <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Este campo estático está disponível para todas as páginas em seu aplicativo.</p>
</li>
<li><p>Clique com o botão direito do mouse no projeto do aplicativo Windows Phone, clique em <strong>Adicionar</strong>, clique em <strong>Serviço Conectado...</strong>, selecione o serviço móvel que você acabou de criar e, em seguida, clique em <strong>OK</strong>. </p>
<p>O mesmo código é adicionado ao arquivo App.xaml.cs compartilhado, mas desta vez dentro de um bloco de compilação condicional do aplicativo Windows Phone.</p></li>
</ol>

Neste momento, ambos os aplicativos do Windows Store e Windows Phone Store são conectados ao novo serviço móvel. A próxima etapa é criar uma nova tabela TodoItem no serviço móvel.

##<a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Novas tabelas são criadas com as colunas Id, __createdAt, __updatedAt e __version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Testar o serviço móvel hospedado no Azure

Agora podemos testar ambos os serviços do aplicativo Windows universal em relação ao serviço móvel hospedado no Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>No <a href="https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento</a>, clique em <strong>Serviços Móveis</strong> e clique no seu serviço móvel.<p></li>
<li><p>Clique na guia <strong>Dados</strong> e, em seguida, clique em <strong>Procurar</strong>.</p>
<p>Observe que a tabela <strong>TodoItem</strong> agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Windows universal para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
  
<!-- Anchors. -->

[Baixar o aplicativo da Windows Store]: #download-app
[Criar o serviço móvel no Visual Studio]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar o serviço móvel]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Exibir os dados carregados no Portal de Gerenciamento do Azure]: #view-data
[Próximas etapas]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Validar e modificar dados com scripts]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[Refinar consultas com paginação]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkID=510826

[Classe de MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
\n<!--HONumber=42-->
