<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Xamarin.iOS. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Xamarin.iOS. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-xamarin-ios">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo Xamarin.iOS][Baixar o projeto do aplicativo Xamarin.iOS]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Este tutorial requer [Componente dos Serviços Móveis do Azure][Componente dos Serviços Móveis do Azure], [XCode 5.0][XCode 5.0], [Xamarin.iOS] e iOS 5.0 ou versões posteriores.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][Baixar o projeto do aplicativo Xamarin.iOS], que é um aplicativo Xamarin.iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo Guia de início rápido do Xamarin.iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória.

1.  Baixe o [aplicativo GetStartedWithData][Baixar o projeto do aplicativo Xamarin.iOS].

2.  Na Xamarin Studio, abra o projeto baixado e examine a classe **TodoService**.

    Observe que há vários comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3.  Vá para o menu **Executar** e escolha **Iniciar sem Depurar** para iniciar o aplicativo.

4.  No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

    ![][0]

    Observe que o texto salvo é exibido na lista abaixo.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Clique na guia **Dados** e, em seguida, clique em **+Criar**.

    ![][1]

    Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3.  No **Nome da tabela**, digite *TodoItem* e, em seguida, clique no botão de verificação.

    ![][2]

    Isso cria uma nova tabela de armazenamento **TodoItem** com o conjunto de permissões padrão, o que significa que qualquer usuário do aplicativo pode acessar e modificar dados na tabela.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>O mesmo nome de tabela &eacute; usado no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. No entanto, cada tabela &eacute; criada em um esquema espec&iacute;fico para um determinado servi&ccedil;o m&oacute;vel. Isso &eacute; para evitar colis&otilde;es de dados quando v&aacute;rios servi&ccedil;os m&oacute;veis usam o mesmo banco de dados.</p> 
</div>

4.  Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5.  Clique na guia **Colunas** e verifique se há uma única coluna **id**, que é criada automaticamente para você.

    Este é o requisito mínimo para uma tabela nos Serviços Móveis.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando o esquema din&acirc;mico estiver ativado no seu servi&ccedil;o m&oacute;vel, novas colunas ser&atilde;o criadas automaticamente quando objetos JSON forem enviados para o servi&ccedil;o m&oacute;vel por uma opera&ccedil;&atilde;o de inser&ccedil;&atilde;o ou atualiza&ccedil;&atilde;o.</p>
</div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  Se você ainda não tiver os **Serviços Móveis do Azure** listados na pasta Componentes, será possível obtê-los clicando com o botão direito do mouse em **Componentes**, escolhendo **Obter mais componentes** e procurando **Serviços Móveis do Azure**.

2.  No modo de exibição Solução do Xamarin Studio, abra a classe **TodoService** e remova o comentário da seguinte instrução`using`:

        using Microsoft.WindowsAzure.MobileServices;

3.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][3]

5.  Na classe **Constantes**, remova o comentário das seguintes constantes:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6.  Substitua **AppUrl** e **AppKey** nas constantes acima com os valores encontrados no Portal de Gerenciamento.

7.  Na classe **TodoService**, remova o comentário da seguinte linha de código:

        private MobileServiceClient client;

    Isso cria uma propriedade que representa o MobileServiceClient que se conecta ao serviço

8.  Na classe **TodoService**, remova o comentário da seguinte linha de código:

        private IMobileServiceTable<TodoItem> todoTable;  

    Isso cria uma representação de propriedade para a tabela de serviços móveis.

9.  Remova o comentário das seguintes linhas no construtor **TodoService**:

        // TODO:: Uncomment these lines to use Mobile Services
        client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
        todoTable = client.GetTable<TodoItem>(); 

    Isso cria uma instância do cliente dos Serviços Móveis e cria a instância da tabela TodoItem.

10. Remova o comentário das seguintes linhas no método **RefreshDataAsync** em **TodoService**

        // TODO:: Uncomment these lines to use Mobile Services

    	tente
     	{
     	// Este código atualiza as entradas na exibição da lista ao consultar a tabela TodoItems.
     	// A consulta exclui completed TodoItems
     	Items = await todoTable
     	.Where (todoItem =\> todoItem.Complete == false).ToListAsync();
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

12. Localize o método **CompleteItemAsync** e remova o comentário da seguinte linha:

        await todoTable.UpdateAsync(item);

    Este código remove TodoItems depois que eles estiverem marcados como concluídos.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

1.  No Xamarin Studio, selecione um dispositivo ou emulador para implantar em uma das caixas de combinação principais e vá para o menu **Executar** e selecione **Iniciar sem Depurar** para iniciar o aplicativo.

    Isso executa seu cliente dos Serviços Móveis do Azure, criado com o Xamarin.iOS, que consulta itens em seu serviço móvel.

2.  Como antes, digite texto na caixa de texto e clique no botão **+**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][4]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Xamarin.iOS.

## Obtenha o exemplo concluído

Baixe o [o projeto de exemplo concluído][Baixar o projeto do aplicativo Xamarin.iOS]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

 
 
<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->

  [Baixar o projeto do aplicativo Xamarin.iOS]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios
