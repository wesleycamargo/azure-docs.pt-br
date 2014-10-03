<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introdução aos dados nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">    
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar os dados em um aplicativo Xamarin.Android. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Xamarin.Android. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/en-us/develop/mobile/tutorials/get-started-xamarin-android">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo Xamarin.Android][]
2.  [Criar o serviço móvel][]
3.  [Adicionar uma tabela de dados para armazenamento][]
4.  [Atualizar o aplicativo para usar Serviços Móveis][]
5.  [Testar o aplicativo com os Serviços Móveis][]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

Este tutorial requer o [Componente dos Serviços Móveis do Azure][], [Xamarin.Android] e Android SDK 4.2 ou uma versão mais recente.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O projeto GetStartedWithData baixado requer o Android 4.2 ou uma vers&atilde;o mais recente. No entanto, o SDK dos Servi&ccedil;os M&oacute;veis requer apenas o Android 2.2 ou uma vers&atilde;o mais recente.</p>
</div>

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][Baixar o projeto do aplicativo Xamarin.Android], que é um aplicativo Xamarin.Android. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis Android, com a exceção de que os itens são armazenados localmente na memória.

1.  Baixe o aplicativo de exemplo `GetStartedWithData` e extraia os arquivos no seu computador.

2.  No Xamarin Studio, clique em **Arquivo** e, em seguida, **Abrir**; navegue até o local em que você extraiu o projeto função Web GetStartedWithData, selecione **XamarinTodoQuickStart.Android.sln** e abra-o.

3.  Localize e abra a classe **TodoActivity**

    Observe que há comentários `// TODO::` que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

4.  No menu **Executar**, clique em **Iniciar sem depurar**; em seguida, você receberá uma solicitação para escolher um emulador ou um dispositivo USB Android anexado.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>&Eacute; poss&iacute;vel executar este projeto usando um telefone Android ou o emulador Android. A execu&ccedil;&atilde;o com um telefone Android exige que voc&ecirc; baixe um driver USB espec&iacute;fico de telefone.</p> <p>Para executar o projeto no emulador Android, deve-se definir no m&iacute;nimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.</p></div>

5.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Adicionar**.

    ![][]

    Observe que o texto salvo é armazenado em uma coleção na memória e exibido na lista abaixo.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela.

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

    Isso adiciona o componente SDK dos Serviços Móveis ao projeto.

2.  Abra o arquivo **AndroidManifest.xml** e certifique-se de que a linha de permissão a seguir exista:

        <uses-permission android:name="android.permission.INTERNET" />

    Isso permite que o aplicativo acesse os Serviços Móveis no Azure.

3.  Na janela **Solução**, abra a classe **TodoActivity** e remova os comentários da linha de código a seguir:

        using Microsoft.WindowsAzure.MobileServices;

4.  Removeremos a lista na memória usada atualmente pelo aplicativo, portanto, é possível substituí-la por um serviço móvel. Na classe **TodoActivity**, comente a seguinte linha de código que define a lista **todoItemList** existente.

        public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5.  Quando a etapa anterior estiver concluída, o projeto indicará erros de compilação. Pesquise os três locais restantes onde a variável `todoItemList` é usada e comente as seções indicadas.

6.  Agora adicionamos o nosso serviço móvel. Remova os comentários das seguintes linhas de código:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

8.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][3]

    Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

9.  Na classe **Constantes**, remova os comentários das variáveis de membro a seguir:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

10. Substitua **AppUrl** e **AppKey** nas variáveis acima com os valores recuperados do Portal de Gerenciamento acima.

11. No método **onCreate**, remova os comentários das seguintes linhas de código que definem a variável **MobileServiceClient**:

        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
        client = new MobileServiceClient(
            Constants.ApplicationURL,
            Constants.ApplicationKey).WithFilter(filter);

        // Get the Mobile Service Table instance to use
        todoTable = client.GetTable<TodoItem>();    

    Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel. Também cria a instância MobileServiceTable que é usada para armazenamento de dados de proxy no serviço móvel.

12. Localize a classe ProgressFilter na parte inferior do arquivo e remova os comentários dela. Essa classe exibe um indicador 'loading' enquanto o MobileServiceClient está executando operações de rede.

13. Remova os comentários destas linhas do método **CheckItem**:

        try {
            await todoTable.UpdateAsync(item);
            if (item.Complete)
                adapter.Remove(item);
        } catch (Exception e) {
            CreateAndShowDialog(e, "Error");
        }

    Isso envia uma atualização de item ao serviço móvel e remove os itens selecionados do adaptador.

14. Remova os comentários destas linhas do método **AddItem**:

        try 
        {
            // Insert the new item
            await todoTable.InsertAsync(item);

            if (!item.Complete) 
                adapter.Add(item);          
        } 
        catch (Exception e) 
        {
            CreateAndShowDialog(e, "Error");
        }           

    Esse código cria um novo item e o insere na tabela no serviço móvel remoto.

15. Remova os comentários destas linhas do método **RefreshItemsFromTableAsync**:

        try {
            // Get the items that weren't marked as completed and add them in the adapter
            var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

            adapter.Clear();

            foreach (TodoItem current in list)
                adapter.Add(current);
        } 
        catch (Exception e) 
        {
            CreateAndShowDialog(e, "Error");
        }

    Isso consulta o serviço móvel e retorna todos os itens que não estão marcados como concluídos. Os itens são adicionados ao adaptador para vinculação.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

1.  No menu **Executar**, clique em **Iniciar sem depuração** para iniciar o projeto. Você será solicitado a selecionar uma imagem existente do emulador ou um dispositivo USB Android conectado.

    Isso executa seu aplicativo criado com Xamarin.Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

2.  Como antes, digite texto significativo e clique em **Adicionar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][4]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Xamarin.Android.

## Obtenha o exemplo concluído

Baixe o [o projeto de exemplo concluído][Baixar o projeto do aplicativo Xamarin.Android]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Xamarin.Android para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um destes outros tutoriais do Xamarin.Android:

-   [Introdução à autenticação][]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->

  [C# da Windows Store]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# da Windows Store"
  [JavaScript da Windows Store]: /en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript da Windows Store"
  [Windows Phone]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
  [Baixar o projeto do aplicativo Xamarin.Android]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5
  [Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
<!-- Images. -->
  []: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [Refinar consultas com paginação]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
