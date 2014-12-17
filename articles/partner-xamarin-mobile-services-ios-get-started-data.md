<properties urlDisplayName="Get Started with Data" pageTitle="Introdução aos dados (Xamarin.iOS) - Serviços Móveis do Azure" metaKeywords="dados do Xamarin.iOS do Azure, dados de serviços móveis do Azure " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Adicionar Serviços Móveis a um aplicativo existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Xamarin.iOS. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criar[a um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observação</b>
<p>O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Xamarin.iOS. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-xamarin-ios">Introdução aos Serviços Móveis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo Xamarin.iOS][GitHub] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

Este tutorial requer o [Componente dos Serviços Móveis do Azure], [XCode 5.0][Install Xcode], [Xamarin.iOS] e iOS 5.0 ou versões posteriores.

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithData][GitHub], que é um aplicativo Xamarin.iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo Guia de início rápido do Xamarin.iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória. 

1. Baixe o [aplicativo GetStartedWithData][GitHub]. 

2. No Xamarin Studio, abra o projeto baixado e examine a classe **TodoService**.

   	Observe que há vários comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3. Vá para o menu **Executar** e escolha **Iniciar sem Depurar** para iniciar o aplicativo.

4. No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

   	![][0]  

   	Observe que o texto salvo é exibido na lista abaixo.

<h2><a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel</h2>

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

2. Clique na guia **Dados** e clique em **+Criar**.

   	![][5]

   		Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Em **Nome da tabela**, digite _TodoItem_ e clique no botão de seleção.

  	![][6]

Isso cria uma nova tabela de armazenamento **TodoItem** com o conjunto de permissões padrão, o que significa que qualquer usuário do aplicativo pode acessar e alterar dados na tabela. 

    <div class="dev-callout"> 
	<b>Observação</b> 
	<p>O mesmo nome de tabela é usado no Guia de início rápido dos Serviços Móveis. No entanto, cada tabela é criada em um esquema específico para um determinado serviço móvel. Isso servirá para evitar colisões de dados quando vários serviços móveis usarem o mesmo banco de dados.</p> 
	</div>

4. Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5. Clique na guia **Colunas** e verifique se há uma única coluna **id**, que é criada automaticamente para você.

Este é o requisito mínimo para uma tabela nos Serviços Móveis. 

    <div class="dev-callout"><b>Observação</b>
	<p>Quando o esquema dinâmico estiver ativado no seu serviço móvel, novas colunas serão criadas automaticamente quando objetos JSON forem enviados para o serviço móvel por uma operação de inserção ou atualização.</p>
    </div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

<h2><a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados</h2>

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local. 

1. Se você ainda não tiver os **Serviços Móveis do Azure** listados na pasta Componentes, você pode obtê-lo clicando em **Componentes**, escolhendo **Obter Mais Componentes** e procurando **Serviços Móveis do Azure**.

2. No modo de exibição Solução do Xamarin Studio, abra a classe **TodoService** e remova o comentário da seguinte instrução `using`:

        using Microsoft.WindowsAzure.MobileServices;
               
3. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

4. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

5. Na classe **Constantes**, remova o comentário das seguintes constantes:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Substitua **AppUrl** e **AppKey** nas constantes acima com os valores encontrados no Portal de Gerenciamento.

7. Na classe **TodoService**, remova o comentário da seguinte linha de código:

        private MobileServiceClient client;

   	Isso cria uma propriedade que representa o MobileServiceClient que se conecta ao serviço

8. Na classe **TodoService**, remova o comentário da seguinte linha de código:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Isso cria uma representação de propriedade para a tabela de serviços móveis.

9. Remova o comentário das seguintes linhas no construtor **TodoService**:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
		todoTable = client.GetTable<TodoItem>(); 

    Isso cria uma instância do cliente dos Serviços Móveis e cria a instância da tabela TodoItem.

10. Remova o comentário das seguintes linhas no método **RefreshDataAsync** em **TodoService**

		// TODO:: Uncomment these lines to use Mobile Services
    try 
        {
			// This code refreshes the entries in the list view by querying the TodoItems table.
			// The query excludes completed TodoItems
			Items = await todoTable
				.Where (todoItem => todoItem.Complete == false).ToListAsync();
		} 
        catch (MobileServiceInvalidOperationException e) 
        {
			Console.Error.WriteLine (@"ERROR {0}", e.Message);
			return null;
		}
        
    Isso cria uma consulta para retornar todas as tarefas que ainda não foram concluídas.

11. Localize o método **InsertTodoItemAsync** e remova o comentário da seguinte linha:

		await todoTable.InsertAsync(todoItem);
		
    Esse código envia uma solicitação de inserção ao serviço móvel.

13. Localize o método **CompleteItemAsync** e remova o comentário da seguinte linha:

		await todoTable.UpdateAsync(item);
		
   	Este código remove TodoItems depois que eles estiverem marcados como concluídos. 

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel</h2>

1. No Xamarin Studio, selecione um dispositivo ou emulador para implantar em uma das caixas de combinação principais e vá para o menu **Executar** e selecione **Iniciar sem Depurar** para iniciar o aplicativo.

   	Isso executa seu cliente dos Serviços Móveis do Azure, criado com o Xamarin.iOS, que consulta itens em seu serviço móvel.

2. Como antes, digite texto na caixa de texto e clique no botão **+**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e clique no seu serviço móvel.

4. Clique na guia **Dados** e clique em **Procurar**.

   	![][9]
  
Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Xamarin.iOS.

## Obtenha o exemplo concluído
Baixe o [projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure. 

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis. 

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

* [Introdução à autenticação] 
	<br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->

[Baixar o aplicativo da Windows Store]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Repositório GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/

[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331302
