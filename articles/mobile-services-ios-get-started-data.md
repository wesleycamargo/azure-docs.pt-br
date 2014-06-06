<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Introdução a dados" pageTitle="Introdução a dados (iOS) | Mobile Dev Center" metaKeywords="Dados iOS do Azure, dados de Serviços Móveis do Azure, " description="Saiba como começar a usar os Serviços Móveis para utilizar dados em seu aplicativo iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução a dados nos Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# Começar a trabalhar com dados em serviços móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>  
</div>	

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo iOS. Neste tutorial, você irá baixar um aplicativo que armazena dados na memória, criar um novo serviço móvel, integrar o serviço móvel com o aplicativo e fazer logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observação</b>
<p>O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo iOS. Desse modo, este tópico explica muitas das etapas que são concluídas para você no início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-ios">Introdução aos Serviços Móveis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo iOS] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

Este tutorial requer o [SDK do iOS para Serviços Móveis], o [XCode 4.5][Install Xcode] e o iOS 5.0 ou posterior.

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>
 
Este tutorial é baseado no [aplicativo GetStartedWithData][GitHub], que é um aplicativo iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido do iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória. 

1. Baixe o [aplicativo de exemplo][GitHub] GetStartedWithData. 

2. No Xcode, abra o projeto baixado e examine o arquivo QSTodoService.m.

   	Observe que há oito comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3. Pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

   	![][0]  

   	Observe que o texto salvo é exibido na lista abaixo.

<h2><a name="create-service"></a><span class="short-header">Criar serviço móvel</span>Criar um novo serviço móvel no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicionar uma nova tabela ao serviço móvel</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados</h2>

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local. 

1. Se você ainda não tiver instalado o [SDK do iOS dos Serviços Móveis], instale-o agora.

2. No Navegador de Projetos no Xcode, abra os arquivos TodoService.m e TodoService.h localizados na pasta Quickstart e adicione a seguinte instrução de importação: 

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3. No arquivo ToDoService.h, localize a seguinte linha de código comentada:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	Após esse comentário, adicione a seguinte linha de código:

        @property (nonatomic, strong)   MSClient *client;

   	Isso cria uma propriedade que representa o MSClient que se conecta ao serviço

4. No arquivo TodoService.m, localize a seguinte linha de código comentada:

        // Create an MSTable property for your items. 

   	Após esse comentário, adicione a seguinte linha de código dentro da declaração @interface:

        @property (nonatomic, strong)   MSTable *table;

   	Isso cria uma representação de propriedade para a tabela de serviços móveis.

5. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

6. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

7. De volta no Xcode, abra TodoService.m e localize a seguinte linha de código comentada:

        // Initialize the Mobile Service client with your URL and key.

    Após esse comentário, adicione a seguinte linha de código:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Isso cria uma instância do cliente de Serviços Móveis.

8. Substitua os valores de **APPURL** e **APPKEY** neste código pela URL e a chave do aplicativo do serviço móvel que você adquiriu na etapa 6.

9. Localize a seguinte linha de código comentada:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Após esse comentário, adicione a seguinte linha de código:

        self.table = [self.client tableWithName:@"TodoItem"];

    Isso cria a instância da tabela TodoItem.

10. Localize a seguinte linha de código comentada:

 	    // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Após esse comentário, adicione a seguinte linha de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Isso cria uma consulta para retornar todas as tarefas que ainda não foram concluídas.

11. Localize a seguinte linha de código comentada:

        // Query the TodoItem table and update the items property with the results from the service.

   	Substitua o comentário e a invocação do bloco **completion** subsequente pelo seguinte código:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

12. Localize o método **addItem** e substitua o corpo do método pelo seguinte código:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
 
            // Let the caller know that we finished
            completion(index);
        }];

    Esse código envia uma solicitação de inserção ao serviço móvel.

13. Localize o método **completeItem** e substitua o corpo do método pelo seguinte código:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
    
            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
    
            [mutableItems removeObjectAtIndex:index];
    
            // Let the caller know that we have finished
            completion(index);
	    }]; 

   	Este código remove TodoItems depois que eles estiverem marcados como concluídos. 

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo com o novo serviço móvel</h2>

1. No Xcode, selecione um emulador no qual implantar (iPhone ou iPad), pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

   	Isso executa seu cliente de Serviços Móveis do Azure, criado com o SDK do iOS, que consulta itens em seu serviço móvel.

2. Como antes, digite texto na caixa de texto e clique no botão **+**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e seu serviço móvel.

4. Clique na guia **Dados** e  **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para corresponderem à classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para iOS.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis. 

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Aprenda a usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

* [Introdução à autenticação] 
	<br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
[Baixar o projeto do aplicativo iOS]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK do iOS para Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Repositório GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784

