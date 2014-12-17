<properties urlDisplayName="Get Started with Data" pageTitle="Introdução aos dados (Xamarin.Android) - Serviços Móveis do Azure" metaKeywords="dados do Xamarin.Android do Azure, dados de serviços móveis do Azure" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="donnam" manager="dwrede" services="mobile-services" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Adicionar Serviços Móveis a um aplicativo existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Xamarin.Android. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criar[a um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.</p>

<div class="dev-callout"><b>Observação</b>
<p>O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Xamarin.Android. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-xamarin-android">Introdução aos Serviços Móveis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo Xamarin.Android][GitHub] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

Este tutorial requer o [Componente dos Serviços Móveis do Azure], [Xamarin.Android] e Android SDK 4.2 ou uma versão posterior. 

<div class="dev-callout"><b>Observação</b>
<p>O projeto GetStartedWithData baixado requer o Android 4.2 ou uma versão posterior. No entanto, o SDK dos Serviços Móveis requer apenas o Android 2.2 ou uma versão posterior.</p>
</div>

<h2><a name="download-app"></a>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithData][GitHub], que é um aplicativo Xamarin.Android. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis Android, com a exceção de que os itens são armazenados localmente na memória. 

1. Baixe o aplicativo de exemplo `GetStartedWithData` e extraia os arquivos em seu computador. 

2. No Xamarin Studio, clique em **Arquivo** e em **Abrir**, navegue até o local onde você extraiu o projeto de exemplo GetStartedWithData, selecione **XamarinTodoQuickStart.Android.sln** e abra-o.

3. Localize e abra a classe **TodoActivity**

   	Observe que há comentários `// TODO::` que especificam as etapas necessárias para fazer com que esse aplicativo funcione com o serviço móvel.

5. No menu **Executar**, clique em **Iniciar sem Depurar**. Em seguida, você receberá uma solicitação para escolher um emulador ou um dispositivo USB Android conectado.

	<div class="dev-callout"><strong>Observação</strong> <p>Você pode executar esse projeto usando um telefone Android ou o emulador Android. A execução com um telefone Android exige que você baixe um driver USB específico de telefone.</p> <p>Para executar o projeto no emulador Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador AVD para criar e gerenciar esses dispositivos.</p></div>

6. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e clique em **Adicionar**.

   	![][13]

   	Observe que o texto salvo é armazenado em uma coleção na memória e exibido na lista abaixo.

<h2><a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel</h2>

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela.  

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

  	Isso adiciona o componente SDK dos Serviços Móveis ao projeto.

2. Abra o arquivo **AndroidManifest.xml** e verifique se existe a seguinte linha de permissão:

		<uses-permission android:name="android.permission.INTERNET" />

  	Isso permite que o aplicativo acesse os Serviços Móveis no Azure.

3. Na janela **Solução**, abra a classe **TodoActivity** e de classe, e remova o comentário da seguinte linha de código: 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Removeremos a lista na memória usada atualmente pelo aplicativo, portanto, é possível substituí-la por um serviço móvel. Na classe **TodoActivity**, comente a seguinte linha de código que define a lista **todoItemList** existente.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Quando a etapa anterior estiver concluída, o projeto indicará erros de compilação. Pesquise os três locais restantes onde a variável `todoItemList` é usada e comente as seções indicadas. 

6. Agora adicionamos o nosso serviço móvel. Remova os comentários das seguintes linhas de código:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

8. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

9. Na classe **Constantes**, remova o comentário das seguintes variáveis de membro:

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

  	Isso cria uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel. Também cria a instância MobileServiceTable que é usada para armazenamento de dados de proxy no serviço móvel.

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

	Isso consulta o serviço móvel e retorna todos os itens que não estão marcados como concluídos. Os itens são adicionados ao adaptador para associação.
		

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel</h2>

1. No menu **Executar**, clique em **Iniciar sem Depurar** para iniciar o projeto. Você será solicitado a selecionar uma imagem existente do emulador ou um dispositivo USB Android conectado.

	Isso executa seu aplicativo criado com Xamarin.Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como feito anteriormente, digite um texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e clique no seu serviço móvel.

4. Clique na guia **Dados** e clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Xamarin.Android.

## Obtenha o exemplo concluído
Baixe o [projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure. 

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Xamarin.Android para trabalhar com dados nos Serviços Móveis. 

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um destes outros tutoriais do Xamarin.Android:

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





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Baixar o projeto do aplicativo Android]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[SDK do Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331302
