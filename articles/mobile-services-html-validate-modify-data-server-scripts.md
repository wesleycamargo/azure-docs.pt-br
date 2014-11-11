<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Validar e modificar dados em Serviços Móveis usando scripts de servidor

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo HTML para tirar proveito desses novos comportamentos.

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
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Esse script verifica o comprimento da propriedade **TodoItem.text** e envia uma resposta de erro quando o comprimento excede 10 caracteres. Caso contrário, a função **execute** será chamada para concluir a inserção.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Voc&ecirc; pode remover um script registrado na guia <strong>Script</strong> clicando em <strong>Limpar</strong> e, em seguida, em <strong>Salvar</strong>.</p></div>

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1.  Execute um dos seguintes arquivos de comando da subpasta **server** do projeto que você modificou quando concluiu o tutorial sobre [Introdução aos dados][Introdução a dados].

    -   **launch-windows** (computadores com Windows)
    -   **launch-mac.command** (computadores com Mac OS X)
    -   **launch-linux.sh** (computadores com Linux)

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que voc&ecirc; confirme se deseja executar o script. Seu navegador da web poder&aacute; avis&aacute;-lo para n&atilde;o executar o script por ele ter sido baixado da internet. Quando isso acontecer, voc&ecirc; dever&aacute; solicitar que o navegador continue para carregar o script.</p>
</div>

    Isso iniciará um servidor web no computador local para hospedar o aplicativo.

2.  Abra o arquivo app.js e substitua o manipulador de eventos **$('\#add-item').submit()** pelo seguinte código:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  Em um navegador da web, navegue até [http://localhost:8000 /][http://localhost:8000 /], digite texto em **Adicionar nova tarefa** e clique em **Adicionar**.

    Observe que a operação falhará e o tratamento de erros exibirá a resposta de erro em uma caixa de diálogo.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você irá atualizar os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>A propriedade de carimbo de data/hora <b>createdAt</b> demonstrada aqui agora &eacute; redundante. Os Servi&ccedil;os M&oacute;veis criam automaticamente uma propriedade do sistema <b>__createdAt</b> para cada tabela.</p>
</div>

1.  Na guia **Scripts** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], substitua o script **Insert** atual pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Essa função aumenta o script de inserção anterior adicionando uma nova propriedade de carimbo de data/hora **createdAt** ao objeto antes que ele seja inserido pela chamada para **request**.**execute**.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O esquema din&acirc;mico deve ser habilitado na primeira vez que esse script de inser&ccedil;&atilde;o for executado. Com o esquema din&acirc;mico habilitado, os Servi&ccedil;os M&oacute;veis automaticamente adicionam a coluna <strong>createdAt</strong> &agrave; tabela <strong>TodoItem</strong> na primeira execu&ccedil;&atilde;o. Por padr&atilde;o, o esquema din&acirc;mico &eacute; habilitado para um novo servi&ccedil;o m&oacute;vel e deve ser desabilitado antes que o aplicativo seja publicado.</p>
</div>

2.  No navegador da web, recarregue a página e digite texto (menos de 10 caracteres) em **Adicionar nova tarefa** e clique em **Adicionar**.

    Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3.  De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.

    Observe que agora há uma coluna **createdAt**, e o novo item inserido tem um valor de carimbo de data/hora.

Em seguida, você precisa atualizar o aplicativo para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do serviço móvel irá ignorar todos os dados em uma resposta que não possam ser serializados em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1.  No editor, abra o arquivo app.js e substitua a função **refreshTodoItems** pelo seguinte código:

        function refreshTodoItems() {
            var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

            query.read().then(function(todoItems) {
                var listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">')
                            .prop('checked', item.complete))
                        .append($('<div>').append($('<input class="item-text">').val(item.text))
                        .append($('<span class="timestamp">' 
                            + (item.createdAt && item.createdAt.toDateString() + ' '
                            + item.createdAt.toLocaleTimeString() || '') 
                            + '</span>')));

                });

                $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
            });
        }

    Isso exibe a parte da data da nova propriedade **createdAt**.

2.  No editor, abra o arquivo style.css e substitua os estilos na classe `item-text` pelo seguinte:

        .item-text { width: 70%; height: 26px; line-height: 24px; 
            border: 1px solid transparent; background-color: transparent; }
        .timestamp { width: 30%; height: 40px; font-size: .75em; }

    Isso redimensiona a caixa de texto e aplica o estilo ao novo texto de carimbo de data/hora.

3.  Recarregue a página.

    Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

4.  Novamente na função **refreshTodoItems**, substitua a linha de código que define a consulta pelo seguinte:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

    Essa função atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.

5.  Recarregue a página.

    Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação][Refinar consultas com paginação].

Para obter mais informações, consulte [Trabalhar com scripts de servidor][Trabalhar com scripts de servidor] e [Referência conceitual ao tutorial do .NET dos Serviços Móveis][Referência conceitual ao tutorial do .NET dos Serviços Móveis]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
  [Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
  [Adicionar um carimbo de data/hora na inserção]: #add-timestamp
  [Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [http://localhost:8000 /]: http://localhost:8000/
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html
  [Trabalhar com scripts de servidor]: /pt-br/develop/mobile/how-to-guides/work-with-server-scripts
  [Referência conceitual ao tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client
