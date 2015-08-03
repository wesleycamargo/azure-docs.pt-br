
##<a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada

1. No Visual Studio, abra o arquivo default.html em seu projeto de início rápido, localize o elemento **button** chamado `buttonRefresh` e adicione o seguinte elemento novo logo depois dele: 

		<button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

	Isso adiciona um novo botão à página.

2. Abra o arquivo de código default.js na pasta do projeto `js`, localize a função **refreshTodoItems** e verifique se essa função contém o seguinte código:

	    todoTable.where({ complete: false })
	       .read()
	       .done(function (results) {
	           todoItems = new WinJS.Binding.List(results);
	           listItems.winControl.itemDataSource = todoItems.dataSource;
	       });            

	Isso filtra os itens de forma que os itens concluídos não sejam retornados pela consulta.

3. Depois da função **refreshTodoItems**, adicione o seguinte código:

		var completeAllTodoItems = function () {
		    var okCommand = new Windows.UI.Popups.UICommand("OK");
		
		    // Asynchronously call the custom API using the POST method. 
		    mobileService.invokeApi("completeall", {
		        body: null,
		        method: "post"
		    }).done(function (results) {
		        var message = results.result.count + " item(s) marked as complete.";
		        var dialog = new Windows.UI.Popups.MessageDialog(message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done(function () {
		            refreshTodoItems();
		        });
		    }, function (error) {
		        var dialog = new Windows.UI.Popups
		            .MessageDialog(error.message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done();
		    });
		};

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

	Esse método identifica o evento **Click** do novo botão. O método **InvokeApiAsync** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros.

## <a name="test-app"></a>Testar o aplicativo

1. No Visual Studio, pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

3. Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.

4. Clique no botão **Concluir Tudo**.

  	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

	É exibida uma caixa de diálogo de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

<!---HONumber=July15_HO4-->