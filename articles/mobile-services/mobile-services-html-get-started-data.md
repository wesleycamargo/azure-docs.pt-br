<properties 
	pageTitle="Adicionar Serviços Móveis a um aplicativo existente (HTML 5) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os Serviços Móveis em um aplicativo HTML existente." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Visão geral 

Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar os dados de um aplicativo HTML. Nesse tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

>[AZURE.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo HTML. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].

> [AZURE.IMPORTANT]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data).

###Requisitos adicionais

Você pode hospedar o aplicativo GetStartedWithData em qualquer servidor web. No entanto, para sua conveniência, foram fornecidos scripts para permitir que você execute o aplicativo em `http://localhost:8000`.
 
+ Para usar o localhost, este tutorial requer que você tenha um dos seguintes servidores web em execução no seu computador local:

	+  **No Windows**: IIS Express. O IIS Express é instalado pelo [Microsoft Web Platform Installer].   
	+  **No MacOS X**: Python, que já deve estar instalado.
	+  **No Linux**: Python. Você deve instalar a [versão mais recente do Python]. 
	
	Você pode usar qualquer servidor web para hospedar o aplicativo, mas estes são os servidores web com suporte dos scripts baixados.

+ Um navegador da web que ofereça suporte a HTML5.

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData], que é um aplicativo HTML5. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1. [Baixe os arquivos de projeto do aplicativo HTML][GetStartedWithData app].

2. Em um editor HTML, abra o projeto baixado e examine o arquivo app.js.

   	Observe que os itens adicionados são armazenados em um objeto **Array** na memória (**staticItems**). Quando você atualizar a página, os dados desaparecerão. Eles não são persistidos.

3. Inicie um dos arquivos de comando a seguir da subpasta **server**.

	+ ** launch-windows** (computadores com Windows) 
	+ ** launch-mac.command** (computadores com Mac OS X)
	+ ** launch-linux.sh** (computadores com Linux)

	> [AZURE.NOTE]Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script
	
	Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

4. Abra a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da Web para iniciar o aplicativo.

5. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir nova tarefa** e clique em **Adicionar**.

   	![][0]

   	Observe que o texto salvo é adicionado à matriz **staticItems** e a página é atualizada para exibir o novo item.

##<a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Clique na guia **Dados** e, em seguida, clique em **+Criar**.

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. No **Nome da tabela**, digite _TodoItem_ e, em seguida, clique no botão de verificação.

  	Isso cria a nova tabela de armazenamento **TodoItem** com as permissões padrão definidas. Isso significa que qualquer pessoa com a chave de aplicativo, que é distribuída com seu aplicativo, pode acessar e alterar dados na tabela. A tabela é criada em um esquema específico para um determinado serviço móvel. Isso é para evitar colisões de dados quando vários serviços móveis usam o mesmo banco de dados.

4. Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

	>[AZURE.NOTE]Novas tabelas são criadas com as colunas Id, __createdAt, __updatedAt, e __versão. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

6. Na guia **Configurar**, verifique se `localhost` já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite `localhost` no campo **Nome do host** e clique em **Salvar**.


	> [AZURE.IMPORTANT]Se você implantar o aplicativo quickstart em um servidor web que não seja o localhost, adicione o nome do host do servidor web à lista **Permitir solicitações de nomes de host**. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

3. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4. Clique na guia **Painel** e anote a **URL do Serviço Móvel**, clique em **Gerenciar chaves** e anote a **Chave do aplicativo**.

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

1. No seu editor da web, abra o arquivo de projeto index.html e adicione o seguinte às referências de script da página:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

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

8. Substitua o método **RefreshTodoItems** pelo código a seguir:

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

##<a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

4. Recarregue a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da Web para iniciar o aplicativo.

    > [AZURE.NOTE]Se você precisar reiniciar o servidor da web, repita as etapas da primeira seção.

2. Da mesma forma que antes, digite texto em **Inserir nova tarefa** e, em seguida, clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5. No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**.

  	Observe que o valor completo foi alterado de **false** para **true**.

6. No arquivo de projeto app.js, localize o método **RefreshTodoItems** e substitua a linha do código que define `query` pelo seguinte:

   		var query = todoItemTable.where({ complete: false });

7. Carregue a página novamente e verifique um outro item da lista.

   	Observe que o item marcado agora desaparecerá da lista. Cada atualização resultará em uma viagem de ida e volta para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas de como habilitar um aplicativo HTML para trabalhar com dados nos Serviços Móveis. A seguir, aprenda a autenticar usuários do seu aplicativo. Experimente um destes outros tutoriais após concluir [Adicionar autenticação ao seu aplicativo]. Se você estiver trabalhando em um projeto de aplicativo Cordova ou PhoneGap, saiba mais sobre as notificações por push em [Notificações por Push para aplicativos Cordova com o Microsoft Azure](https://msdn.microsoft.com/magazine/dn879353.aspx).

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-html-get-started.md
[Adicionar autenticação ao seu aplicativo]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=July15_HO4-->