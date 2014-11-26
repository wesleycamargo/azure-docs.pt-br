<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Windows universal. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone][Criar aplicativos universais do Windows voltados para Windows e Windows Phone].

Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo Windows universal que armazena dados na memória, cria um novo serviço móvel, integra o serviço mível ao aplicativo e entra no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Express 2013 com atualização 3 para Windows para se conectar a um novo serviço móvel para um novo aplicativo Windows universal. As mesmas etapas podem ser usadas para conectar um serviço móvel a um aplicativo para Windows Store ou Windows Phone Store 8.1. Para conectar um serviço móvel a um aplicativo para Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte [Introdução aos dados para Windows Phone][Introdução aos dados para Windows Phone].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   [Visual Studio Express 2013 para Windows][Visual Studio Express 2013 para Windows] (Atualização 2 ou versão posterior).

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Criar um novo serviço móvel a partir do Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>No Gerenciador de Soluções, navegue para a subpasta <b>services\\mobileService\\scripts</b>, abra o arquivo de script service.js e observe a nova variável global, que se parece com o exemplo a seguir:</p>

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

    <p>Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma variável global. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Como uma referência a esse script foi adicionada ao arquivo default.html, esta variável está disponível para todos os arquivos de script que também fazem referência desta página.</p></li>

<li><p>Abra o arquivo do projeto default.html, localize a referência para o novo arquivo de script service.kd e certifique-se de que o caminho em referência é como o seguinte:</p>

<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>

    <p>Existe atualmente um bug no Visual Studio que gera um nome incorreto de pasta no caminho.</p></li>

<li><p>Clique com o botão direito do mouse no projeto do aplicativo Windows Phone, clique em <b>Adicionar</b>, clique em <b>Serviço Conectado...</b>, selecione o serviço móvel que você acabou de criar e, em seguida, clique em <b>OK</b>.</p>

    <p>O mesmo arquivo com o novo código é adicionado ao projeto do aplicativo Windows Phone Store. Certifique-se de corrigir também o caminho de referência adicionado ao arquivo default.html.</p></li>
</ol>

## <a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Novas tabelas são criadas com colunas Id, \_\_createdAt, \_\_updatedAt, and \_\_version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico][Esquema dinâmico].

## <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="test-azure-hosted"></a>Testar o serviço móvel hospedado no Azure

Agora podemos testar ambos os serviços do aplicativo Windows universal em relação ao serviço móvel hospedado no Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>No <a href="https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento</a>, clique em <b>Serviços Móveis</b> e, em seguida, clique em seu serviço móvel.</p></li>

<li><p>Clique na guia <b>Dados</b> e, em seguida, clique em <b>Procurar</b>.</p>

    <p>Observe que a tabela <b>TodoItem</b> agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.</p></li>
</ol>

![][0]

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Windows universal para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]<br/>
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]<br/>
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]<br/>
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]<br/>
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis][Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]<br/>
    Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.



  [Criar aplicativos universais do Windows voltados para Windows e Windows Phone]: http://msdn.microsoft.com/pt-br/library/windows/apps/xaml/dn609832.aspx
  [Introdução aos dados para Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 para Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [Esquema dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx
  [0]: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Refinar consultas com paginação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library/
