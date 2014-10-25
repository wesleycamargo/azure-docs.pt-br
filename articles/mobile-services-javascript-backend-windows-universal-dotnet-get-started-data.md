<properties linkid="mobile-services-javascript-backend-windows-universal-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Universal) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your universal Windows app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Windows universal. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone][Criar aplicativos universais do Windows voltados para Windows e Windows Phone].

Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo Windows universal que armazena dados na memória, cria um novo serviço móvel, integra o serviço mível ao aplicativo e entra no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para se conectar a um novo serviço móvel para um novo aplicativo Windows universal. As mesmas etapas podem ser usadas para conectar um serviço móvel a um aplicativo para Windows Store ou Windows Phone Store 8.1. Para conectar um serviço móvel a um aplicativo para Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte [Introdução aos dados para Windows Phone][Introdução aos dados para Windows Phone].

> Se você não pode atualizar para o Visual Studio Professional 2013 Atualização 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão][esta versão] do tópico.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel no Visual Studio][Criar o serviço móvel no Visual Studio]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar o serviço móvel][Atualizar o aplicativo para usar o serviço móvel]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]
6.  [Exibir os dados carregados no Portal de Gerenciamento do Azure][Exibir os dados carregados no Portal de Gerenciamento do Azure]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   [Visual Studio Express 2013 para Windows][Visual Studio Express 2013 para Windows] (Atualização 2 ou versão posterior).

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Criar um novo serviço móvel a partir do Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  No Solution Explorer, abra o arquivo de código App.xaml.cs na pasta de projeto GetStartedWithData.Shared e avise sobre o novo campo estático que foi adicionado à classe **Aplicativo** dentro do bloco de compilação condicional do aplicativo Windows Store, que parece com o exemplo a seguir:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma instância da [classe MobileServiceClient][classe MobileServiceClient]. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Este campo estático está disponível para todas as páginas em seu aplicativo.

2.  Clique com o botão direito do mouse no projeto do aplicativo Windows Phone, clique em **Adicionar**, clique em **Serviço Conectado...**, selecione o serviço móvel que você acabou de criar e, em seguida, clique em **OK**.

    O mesmo código é adicionado ao arquivo App.xaml.cs compartilhado, mas desta vez dentro de um bloco de compilação condicional do aplicativo Windows Phone.

Neste momento, ambos os aplicativos do Windows Store e Windows Phone Store são conectados ao novo serviço móvel. A próxima etapa é criar uma nova tabela TodoItem no serviço móvel.

## <a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Novas tabelas são criadas com colunas Id, \_\_createdAt, \_\_updatedAt, and \_\_version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico][Esquema dinâmico].

# <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="test-azure-hosted"></a>Testar o serviço móvel hospedado no Azure

Agora podemos testar ambos os serviços do aplicativo Windows universal em relação ao serviço móvel hospedado no Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

    <p>
2.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

![][]

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

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]<br/>
    Saiba mais sobre como usar os Serviços Móveis com o .NET.



  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Criar aplicativos universais do Windows voltados para Windows e Windows Phone]: http://msdn.microsoft.com/pt-br/library/windows/apps/xaml/dn609832.aspx
  [Introdução aos dados para Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data
  [esta versão]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel no Visual Studio]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar o serviço móvel]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Exibir os dados carregados no Portal de Gerenciamento do Azure]: #view-data
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Express 2013 para Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Esquema dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [Refinar consultas com paginação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
