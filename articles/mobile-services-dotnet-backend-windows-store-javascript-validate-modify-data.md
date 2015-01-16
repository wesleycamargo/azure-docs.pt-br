<properties urlDisplayName="Validate and Modify Data" pageTitle="Usar o back-end do .NET para validar e modificar dados (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como validar, modificar e aumentar dados para seu aplicativo Javascript da Windows Store com serviços móveis do Microsoft Azure com back-end do .Net." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc" />

# Validar e modificar dados nos Serviços Móveis usando o back-end do .NET

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como usar código em seu back-end do .Net dos Serviços Móveis do Azure para validar e modificar dados. O serviço de back-end do .NET é um serviço HTTP criado com a estrutura da API Web. Se você estiver familiarizado com a classe `ApiController` definida com a estrutura da API Web, a classe `TableController` fornecida pelos Serviços Móveis será muito intuitiva. A `TableController` é derivada da classe `ApiController` e fornece funcionalidade adicional para fazer a interface com uma tabela do banco de dados. Pode ser usada para executar operações nos dados que estão sendo inseridos e atualizados, incluindo a validação e a modificação de dados demonstradas neste tutorial. 

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]
3. [Validação da duração do teste]
4. [Adicionar um campo de carimbo de data/hora para CompleteDate]
5. [Atualizar o cliente para exibir a CompleteDate]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução] ou [Introdução aos dados](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução] ou [Introdução aos dados](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).  

## <a name="string-length-validation"></a>Adicionar código de validação ao Serviço Móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está configurado para validar os dados e enviar respostas para um tamanho de texto inválido, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação. O erro será captado na chamada do aplicativo cliente para `IMobileServiceTable<TodoItem].InsertAsync()`.

1. Na janela do Gerenciador de Soluções do Visual Studio, navegue para o projeto cliente JavaScript e expanda a pasta **js**. Em seguida, abra o arquivo default.js

2. No default.js, substitua a função **insertTodoItem** existente pela seguinte definição da função:


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	Esta versão da função inclui tratamento de erro e exibe um `MessageDialog` com a mensagem de erro da resposta, o texto do status e o código do status.

## <a name="test-length-validation"></a>Validação da duração do teste

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo cliente JavaScript e clique em **Depurar**, **Iniciar nova instância**.

2. Digite o texto para um novo item de tarefa pendente com um tamanho maior que 10 caracteres e, em seguida, clique em **Salvar**.

    ![][1]

3. Será exibida uma caixa de diálogo de mensagem semelhante à seguinte em resposta ao texto inválido.

    ![][2]

## <a name="add-timestamp"></a>Adicionar um campo de carimbo de data/hora para CompleteDate


[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Atualizar o cliente para exibir a completeDate

A etapa final é atualizar o cliente para exibir os novos dados de **completeDate**. 


1. No Gerenciador de Soluções do Visual Studio, no projeto cliente JavaScript, abra o arquivo default.html. Substitua o elemento da marca `div` do modelo da ligação pela definição abaixo. Em seguida, salve o arquivo. Isso adiciona uma marca `div` com a propriedade innerText ligada a **completeDate**.
	      
        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>



2. No default.js, remova ou comente a função da cláusula `.Where` no método **refreshTodoItems** existente, para que os todoitems concluídos sejam incluídos nos resultados.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };


3. No default.js, atualize a função **updateCheckedTodoItem** da seguinte maneira para que os itens sejam atualizados depois de uma atualização e os itens concluídos não sejam removidos da lista. Em seguida, salve o arquivo.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. Nas janela do Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na **Solução** e clique em **Recompilar Solução** para recompilar o cliente e o serviço de back-end do .NET. Verifique se os dois projetos são compilados sem erros.

    ![][3]
	
5. Pressione a tecla **F5** para executar o aplicativo cliente e o serviço localmente. Adicione alguns novos itens e clique para marcar alguns itens como concluídos, para ver o carimbo de data/hora de **CompleteDate** sendo atualizado.

    ![][4]

6. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço todolist e clique em **Publicar**. Publique o serviço de back-end do .NET no Microsoft Azure usando o arquivo de configuração da publicação que você baixou no portal do Azure.

7. Atualize o arquivo default.js do projeto cliente removendo o comentário da conexão ao endereço do serviço móvel. Teste o aplicativo com o back-end do .NET hospedado em sua conta do Azure.




## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação].

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os tutoriais a seguir:

* [Autorização de usuários do lado do serviço]
  <br/>Saiba como filtrar dados com base na ID de um usuário autenticado.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com .NET.

<!-- Anchors. -->
[Adicionar validação de tamanho da cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para dar suporte à validação]: #update-client-validation
[Testar validação de tamanho]: #test-length-validation
[Adicionar um campo de carimbo de data/hora para CompleteDate]: #add-timestamp
[Atualizar o cliente para exibir o CompleteDate]: #update-client-timestamp
[Próximas etapas]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Autorização de usuários no lado do serviço]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library móveis
