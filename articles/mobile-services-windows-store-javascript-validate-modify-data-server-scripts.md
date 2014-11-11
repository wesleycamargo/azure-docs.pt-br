<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Validar e modificar dados em Serviços Móveis usando scripts de servidor



[C# da Windows Store][C# da Windows Store] [JavaScript da Windows Store][JavaScript da Windows Store] [Windows Phone][Windows Phone][iOS][iOS] [Android][Android] [HTML][HTML] [Xamarin.iOS][Xamarin.iOS] [Xamarin.Android][Xamarin.Android]





[Back-end do .NET][Back-end do .NET] | [Back-end do JavaScript][Back-end do JavaScript]



Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo da Windows Store para tirar proveito desses novos comportamentos.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Adicionar validação de comprimento de cadeia de caracteres][Adicionar validação de comprimento de cadeia de caracteres]
2.  [Atualizar o cliente para oferecer suporte à validação][Atualizar o cliente para oferecer suporte à validação]
3.  [Adicionar um carimbo de data/hora na inserção][Adicionar um carimbo de data/hora na inserção]
4.  [Atualizar o cliente para exibir o carimbo de data/hora][Atualizar o cliente para exibir o carimbo de data/hora]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução a dados][Introdução a dados].

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o comprimento dos dados enviados pelos usuários. Primeiro, você registra um script que valida o comprimento dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][0]

2.  Clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][1]

3.  Clique em **Script** e selecione a operação **Inserir**.

    ![][2]

4.  Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Esse script verifica o comprimento da propriedade **TodoItem.text** e envia uma resposta de erro quando o comprimento excede 10 caracteres. Caso contrário, o método **execute** será chamado para concluir a inserção.

    <div class="dev-callout">

    <strong>Observação</strong>
    Você pode remover um script registrado na guia <strong>Script</strong> clicando em <strong>Limpar</strong> e, em seguida, em <strong>Salvar</strong>.

    </div>

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1.  No Visual Studio 2012 Express para Windows 8, abra o projeto que você modificou quando concluiu o tutorial [Introdução a dados][Introdução a dados].

2.  Pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto com mais de 10 caracteres em **Inserir um TodoItem** e clique em **Salvar**.

    Observe que o aplicativo gera um erro não tratado como resultado da resposta 400 (solicitação incorreta) retornada pelo serviço móvel.

3.  Abra o arquivo default.js e substitua o método **InsertTodoItem** existente pelo seguinte:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Create the error message dialog and set its content to the error
                // message contained in the response.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

    Essa versão do método inclui tratamento de erro que exibe a resposta do erro em uma caixa de diálogo.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você irá atualizar os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

<div class="dev-callout">

<strong>Observação</strong>
A propriedade de carimbo de data/hora <strong>createdAt</strong> demonstrada aqui agora é redundante. Os Serviços Móveis criam automaticamente uma propriedade do sistema <strong>\_\_createdAt</strong> para cada tabela.

</div>

1.  Na guia **Scripts** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], substitua o script **Insert** atual pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Essa função aumenta o script de inserção anterior adicionando uma nova propriedade de carimbo de data/hora **createdAt** ao objeto antes que ele seja inserido pela chamada para **request**.**execute**.

    <div class="dev-callout">

    <strong>Observação</strong>
    O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis automaticamente adicionam a coluna <strong>createdAt</strong> à tabela <strong>TodoItem</strong> na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.

    </div>

2.  No Visual Studio, pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto (menor do que 10 caracteres) em **Inserir um TodoItem** e clique em **Salvar**.

    Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3.  De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.

    Observe que agora há uma coluna **createdAt**, e o novo item inserido tem um valor de carimbo de data/hora.

Em seguida, você precisa atualizar o aplicativo da Windows Store para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do serviço móvel irá ignorar todos os dados em uma resposta que não possam ser serializados em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1.  No Visual Studio, abra o arquivo default.html e adicione o seguinte elemento HTML na grade TemplateItem:

        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

    Isso exibe a nova propriedade **createdAt**.

2.  Pressione a tecla **F5** para executar o aplicativo.

    Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

3.  Abra o arquivo default.js e substitua o método **RefreshTodoItems** existente pelo seguinte código:

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items. 
            todoTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

    Esse método atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.

4.  Pressione a tecla **F5** para executar o aplicativo.

    Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação][Refinar consultas com paginação].

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os tutoriais a seguir:

-   [Autorizar usuários com scripts][Autorizar usuários com scripts]
    Saiba como filtrar dados com base na ID de um usuário autenticado.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "Back-end do JavaScript"
  [Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
  [Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
  [Adicionar um carimbo de data/hora na inserção]: #add-timestamp
  [Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-js
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
