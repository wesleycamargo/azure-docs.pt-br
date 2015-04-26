<properties 
	pageTitle="Introdução aos dados (iOS) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar dados em um aplicativo iOS. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e visualizará as alterações aos dados feitas ao executar o aplicativo.

O serviço móvel que você criará neste tutorial dará suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

>[AZURE.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo iOS. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo iOS]
2. [Criar o serviço móvel]
3. [Baixar o serviço localmente]
4. [Testar o serviço móvel]
5. [Publicar o serviço móvel no Azure]
7. [Atualizar o aplicativo para usar os Serviços Móveis]
8. [Testar o aplicativo com os Serviços Móveis]

Este tutorial exige o seguinte:

+ [SDK do iOS para Serviços Móveis] e [XCode 4.5][Instalar Xcode] e iOS 5.0 ou versões posteriores.
+ Visual Studio 2013 (você pode obter o [Visual Studio Express para Web](http://go.microsoft.com/p/?linkid=9832232) gratuitamente).
+ Uma conta do Microsoft Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial baseia-se-se no [GetStartedWithData][GitHub], que é um aplicativo iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido do iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória.

1. Baixar aplicativo de exemplo [GetStartedWithData][GitHub].

2. No Xcode, abra o projeto baixado e examine o arquivo TodoService.m.

   	Observe que há oito comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3. Pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

   	![][0]  

   	Observe que o texto salvo é exibido na lista abaixo.

##<a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Baixar o serviço no computador local

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Publicar o serviço móvel no Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

1. Em Xcode, selecione um emulador no qual implantar (iPhone ou iPad), pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

   	Isso executa seu cliente de Serviços Móveis do Azure, criado com o SDK do iOS, que consulta itens em seu serviço móvel.

2. Como antes, digite texto na caixa de texto e clique no botão **+**.

   	Isso envia um novo item como uma inserção no serviço móvel. Cada novo todoItem é armazenado e atualizado no banco de dados SQL configurado anteriormente para o serviço móvel no Portal de Gerenciamento do Azure.

3. Pare e reinicie o aplicativo para ver se as alterações foram persistidas para o banco de dados no Azure.

	Você também pode examinar o banco de dados usando o Portal de Gerenciamento do Azure ou o Pesquisador de Objetos do SQL Server. As próximas duas etapas usam o [Portal de Gerenciamento do Azure] para exibir as alterações no banco de dados.

4. No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![][17]

5. No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. Sua consulta será semelhante à consulta a seguir, mas usará seu nome de banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

Isso conclui o tutorial **Introdução aos dados**.

##<a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

* [Introdução à autenticação]
	<br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push]
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
[Baixar o projeto do aplicativo iOS]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar os Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps
[Baixar o serviço localmente]: #download-the-service-locally
[Testar o serviço móvel]: #test-the-service
[Publicar o serviço móvel no Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
[Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Repositório GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784



<!--HONumber=42-->
