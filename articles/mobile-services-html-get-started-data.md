<properties urlDisplayName="Get Started with Data (HTML5)" pageTitle="Introdução aos dados (HTML 5) Adicionar os Serviços Móveis para um aplicativo existente| Centro de Desenvolvimento Móvel" metaKeywords ="" description="Saiba como começar a usar os Serviços Móveis para utilizar dados em seu aplicativo HTML." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Adicionar os Serviços Móveis em um aplicativo existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar os dados de um aplicativo HTML. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

>[WACOM.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo HTML. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial. <a href="/pt-br/develop/mobile/tutorials/get-started-html">Introdução aos Serviços Móveis</a>.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto do aplicativo HTML]
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar os Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

###Requisitos adicionais

Você pode hospedar o aplicativo GetStartedWithData em qualquer servidor web. No entanto, para sua conveniência, foram fornecidos scripts para permitir que você execute o aplicativo em "http://localhost:8000".
 
+ Para usar o localhost, este tutorial requer que você tenha um dos seguintes servidores web em execução no seu computador local:

	+  **No Windows**: IIS Express. O IIS Express é instalado pelo [Microsoft Web Platform Installer].   
	+  **No MacOS X**: Python, que já deve estar instalado.
	+  **No Linux**: Python. Você deve instalar a [versão mais recente do Python]. 
	
	Você pode usar qualquer servidor web para hospedar o aplicativo, mas estes são os servidores web com suporte dos scripts baixados.  

+ Um navegador da web que ofereça suporte a HTML5.

<h2><a name="download-app"></a>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithData], que é um aplicativo HTML5. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória. 

1. [Baixar os arquivos de projeto do aplicativo HTML][GetStartedWithData app].

2. Em um editor HTML, abra o projeto baixado e examine o arquivo app.js.

   	Observe que os itens adicionados são armazenados em um objeto **Array** na memória (**staticItems**). Quando você atualizar a página, os dados desaparecerão. Eles não são persistidos.

3. Inicie um dos arquivos de comando a seguir da subpasta **server**.

	+ ** launch-windows** (computadores com Windows) 
	+ ** launch-mac.command** (computadores com Mac OS X)
	+ ** launch-linux.sh** (computadores com Linux)

	<div class="dev-callout"><b>Observação</b>
		<p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.</p>
	</div>
	
	Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

4. Abrir a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da web para iniciar o aplicativo.

5. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir nova tarefa** e clique em **Adicionar**.

   	![][0]  

   	Observe que o texto salvo é adicionado à matriz **staticItems** e a página é atualizada para exibir o novo item.

<h2><a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel</h2>

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Clique na guia **Dados** e, em seguida, clique em **+Criar**.

   	![][5]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Em **Nome da tabela** digite _TodoItem_ e, em seguida, clique no botão de seleção.

  	![][6]

  	Isso cria a nova tabela de armazenamento **TodoItem** com as permissões padrão definidas. Isso significa que qualquer pessoa com a chave de aplicativo, que é distribuída com seu aplicativo, pode acessar e alterar dados na tabela.

    <div class="dev-callout"> 
	<b>Observação</b> 
	<p>O mesmo nome de tabela é usado no Guia de início rápido dos Serviços Móveis. No entanto, cada tabela é criada em um esquema específico para um determinado serviço móvel. Isso é para evitar colisões de dados quando vários serviços móveis usam o mesmo banco de dados.</p> 
	</div>

4. Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5. Clique na guia **Colunas**. Verifique se as seguintes colunas padrão são criadas automaticamente para você: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nome da coluna</th>
 	<th>Tipo</th>
 	<th>Índice</th>
 	</tr>
 	<tr>
 	<td>ID</td>
 	<td>string</td>
 	<td>Indexada</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>data</td>
 	<td>Indexada</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>data</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>carimbo de data/hora (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Este é o requisito mínimo para uma tabela nos Serviços Móveis. 

    <div class="dev-callout"><b>Observação</b>
	<p>Quando o esquema dinâmico estiver ativado no seu serviço móvel, novas colunas serão criadas automaticamente quando objetos JSON forem enviados para o serviço móvel por uma operação de inserção ou atualização.</p>
    </div>

6. Na guia **Configurar**, verifique se 'localhost' já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite 'localhost' no campo **Nome do host** e clique em **Salvar**.

  	![][11]

	<div class="dev-callout"><b>Observação</b>
		<p>Se você implantar o aplicativo quickstart para um servidor web que não seja localhost, deverá adicionar o nome do host do servidor web à lista <strong>Permitir solicitações de nomes de host</strong> . Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx" target="_blank">Compartilhamento de recursos entre origens</a>.</p>
	</div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

<h2><a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados</h2>

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local. 

3. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

1. No seu editor da web, abra o arquivo de projeto index.html e adicione o seguinte às referências de script da página:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. No editor, abra o arquivo app.js, remova o comentário do código a seguir que define a variável **MobileServiceClient** e forneça a URL e a chave do aplicativo do serviço móvel no construtor **MobileServiceClient**, nessa ordem:

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel.

6. Remova os comentários das linhas de código a seguir:

		var itemCount = 0;
		var staticItems = [];

	Os dados serão armazenados no serviço móvel, e não em uma matriz na memória.

6. Remova os comentários da linha de código a seguir:

        todoItemTable = client.getTable('todoitem');

   	Esse código criará um objeto proxy (**todoItemTable**) para o Banco de Dados SQL **TodoItem**. 

7. Substitua o manipulador de eventos **$('#add-item').submit** pelo seguinte código:

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	Esse código insere um novo item na tabela.

8. Substitua o método **RefreshTodoItems** pelo seguinte código:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   Isso enviará uma consulta para o serviço móvel que retornará todos os itens. Os resultados são iterados, e os dados são exibidos na página. 

9. Substitua os manipuladores de eventos **$(document.body).on('change', '.item-text')** e **$(document.body).on('change', '.item-complete')** pelo seguinte código:
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	Isso enviará uma atualização do item para o serviço móvel quando o texto for alterado ou quando a caixa for selecionada.

10. Substitua o manipulador de eventos **$(document.body).on('click', '.item-delete')** pelo seguinte código:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Ele enviará uma exclusão para o serviço móvel quando o botão **Excluir** for clicado.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel</h2>

4. Recarregue a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da web, inicie o aplicativo.

    <div class="dev-callout"><b>Observação</b>
	<p>Se você precisar reiniciar o servidor da web, repita as etapas da primeira seção.</p>
    </div>

2. Da mesma forma que antes, digite o texto em **Inserir nova tarefa** e, em seguida, clique em **Adicionar**. 

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5. No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**. 

  	Observe que o valor completo foi alterado de **false** para **true**.

6. No arquivo de projeto app.js, localize o método **RefreshTodoItems** e substitua a linha do código que define "query" pelo seguinte:

   		var query = todoItemTable.where({ complete: false });

7. Carregue a página novamente e verifique um outro item da lista.

   	Observe que o item marcado agora desaparecerá da lista. Cada atualização resultará em uma viagem de ida e volta para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas de como habilitar um aplicativo HTML para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.
 
Após concluir a série de dados, saiba como autenticar os usuários do seu aplicativo. Experimente um destes outros tutoriais após concluir [Introdução à autenticação].

<!-- Anchors. -->
[Baixar o projeto do aplicativo HTML]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar os Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Aplicativo GetStartedWithData]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client

[Compartilhamento de recursos entre origens]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx


<!--HONumber=35.2-->
