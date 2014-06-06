<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Introdução a dados - Android" pageTitle="Introdução a dados (Android) | Mobile Dev Center" metaKeywords="dados android Azure, dados de serviços móveis do Azure, windows android, windows droid, microsoft droid, microsoft android" description="Saiba como começar a usar os Serviços Móveis para utilizar dados em seu aplicativo Android" metaCanonical="" services="" documentationCenter="Mobile" title="Introdução a dados nos Serviços Móveis" authors="ricksal,glenga" solutions="" manager="" editor="" />



# Começar a trabalhar com dados em serviços móveis
<div class="dev-center-tutorial-selector sublanding">    
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo Android. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criar[a um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a><span class="time">15:32</span></div>
</div>

<div class="dev-callout"><b>Observação</b>
<p>O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo Android. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-android">Introdução aos Serviços Móveis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo Android] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

Este tutorial requer o [SDK para Android de Serviços Móveis]; o <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">SDK para Android</a> que inclui o IDE (ambiente de desenvolvimento integrado) Eclipse e o plug-in Android Developer Tools (ADT); e o Android 4.2 ou a versão mais recente. 

<div class="dev-callout"><b>Observação</b>
<p>Este tutorial fornece instruções para instalar o SKD para Android e o SDK para Android de Serviços Móveis. O projeto GetStartedWithData baixado requer o Android 4.2 ou uma versão posterior. No entanto, o SDK dos Serviços Móveis requer apenas o Android 2.2 ou uma versão posterior.</p>
</div>



<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

###Obter o código de exemplo

Este tutorial é baseado no [aplicativo GetStartedWithData][GitHub], que é um aplicativo Android. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis Android, com a exceção de que os itens são armazenados localmente na memória. Você adicionará o código necessário para persistir os dados no armazenamento.


1. Baixe o aplicativo de exemplo `GetStartedWithData` e expanda os arquivos em seu computador. 

2. No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android Existente no Espaço de Trabalho** e clique em **Avançar**. 

 	![][14]

3. Clique em **Procurar**, navegue até o local dos arquivos do projeto expandido, clique em **OK** e verifique se o projeto TodoActivity está selecionado. Se desejar que o projeto seja copiado no espaço de trabalho, marque a caixa **Copiar projetos na área de trabalho**. Finalmente, clique em **Concluir**. 

 	![][15]

	Isso importa os arquivos de projeto no espaço de trabalho atual.

###Verifique a versão do SKD para Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Inspecionar e executar o código de exemplo

1. No Explorador de Pacotes, expanda **GetStartedWithData**, **src** e **.com.example.GetStartedWithData** e examine o arquivo ToDoActivity.java.

   	![][12]

   	Observe que há comentários `//TODO` que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

6. No menu **Executar**, clique em **Executar** e clique em **Aplicativo Android** para iniciar o projeto.

	<div class="dev-callout"><strong>Observação</strong> <p>Você pode executar esse projeto usando um telefone Android ou o emulador Android. A execução com um telefone Android exige que você baixe um driver USB específico de telefone.</p> <p>Para executar o projeto no emulador Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador AVD para criar e gerenciar esses dispositivos.</p></div>

7. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique em **Adicionar**.

   	![][13]

   	Observe que o texto salvo é armazenado em uma coleção na memória e exibido na lista abaixo.

<h2><a name="create-service"></a><span class="short-header">Criar serviço móvel</span>Criar um novo serviço móvel no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicionar uma nova tabela ao serviço móvel</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados</h2>

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local. 

1. Se você ainda não tiver o [SDK para Android de Serviços Móveis], baixe-o agora e expanda os arquivos compactados.

2. Copie os arquivos `. jar` da pasta `mobileservices` do SDK para a pasta `libs` do projeto GetStartedWithData.

3. No Explorador de Pacotes no Eclipse, clique na pasta `libs`, clique em **Atualizar**, e os arquivos jar copiados serão exibidos

  	Isso adiciona a referência ao SDK dos Serviços Móveis ao espaço de trabalho.

4. Abra o arquivo AndroidManifest.xml e adicione a linha a seguir, que permite que o aplicativo acesse os Serviços Móveis no Azure.

		<uses-permission android:name="android.permission.INTERNET" />

5. No Explorador de Pacotes, abra o arquivo TodoActivity.java localizado no pacote com.example.getstartedwithdata e remova os comentários das seguintes linhas de código: 

		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
		import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
		import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
		import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

		import java.net.MalformedURLException;
 
6. Removeremos a lista na memória usada atualmente pelo aplicativo, portanto, é possível substituí-la por um serviço móvel. Na classe **ToDoActivity**, comente a seguinte linha de código que define a lista **toDoItemList** existente.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Quando a etapa anterior estiver concluída, o projeto indicará erros de compilação. Pesquise os três locais restantes onde a variável `toDoItemList` é usada e comente as seções indicadas. Remova também `import java.util.ArrayList`. Isso remove totalmente a lista na memória. 

8. Agora adicionamos o nosso serviço móvel. Remova os comentários das seguintes linhas de código:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Localize a classe ProgressFilter na parte inferior do arquivo e remova os comentários dela. Essa classe exibe um indicador 'loading' enquanto o MobileServiceClient está executando operações de rede.


10. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

11. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

12. No método **onCreate**, remova os comentários das seguintes linhas de código que definem a variável **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel. Também cria a instância MobileServiceTable que é usada para armazenamento de dados de proxy no serviço móvel.

13. No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL e pela chave do aplicativo, nessa ordem.



14. Remova os comentários destas linhas do método **checkItem**:

		mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {	
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (entity.isComplete()) {
						mAdapter.remove(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");	
				}
			}
		});

   	Isso envia uma atualização de item ao serviço móvel e remove os itens selecionados do adaptador.
    
15. Remova os comentários destas linhas do método **addItem**:
	
		mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {
			
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (!entity.isComplete()) {
						mAdapter.add(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");
				}				
			}
		});
		

  	Esse código cria um novo item e o insere na tabela no serviço móvel remoto.

16. Remova os comentários destas linhas do método **refreshItemsFromTable**:

		mToDoTable.where().field("complete").eq(false)
		.execute(new TableQueryCallback<ToDoItem>() {
		     public void onCompleted(List<ToDoItem> result, 
		    		 int count, Exception exception, 
		    		 ServiceFilterResponse response) {
						
						if(exception == null){
							mAdapter.clear();
	
							for (ToDoItem item : result) {
								mAdapter.add(item);
							}
						} else {
							createAndShowDialog(exception, "Error");
						}
					}
				}); 

	Isso consulta o serviço móvel e retorna todos os itens que não estão marcados como concluídos. Os itens são adicionados ao adaptador para vinculação.
		


<h2><a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo com o novo serviço móvel</h2>

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SKD para Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como feito anteriormente, digite um texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e clique no seu serviço móvel.

4. Clique na guia **Dados** e clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com alguns valores gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para Android.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis. 

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar os dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Aprenda a usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um destes outros tutoriais do Android:

* [Introdução à autenticação] 
	<br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. -->
[Baixar o projeto do aplicativo Android]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png


[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-android
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK para Android de Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[SKD para Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

