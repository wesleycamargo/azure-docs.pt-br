<properties 
	pageTitle="Usar scripts de servidor para validar e modificar dados (HTML 5) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como validar e modificar dados enviados usando scripts de servidor em seu aplicativo HTML." 
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
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Validar e modificar dados em Serviços Móveis usando scripts de servidor 

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você vai definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também atualizará o aplicativo HTML para tirar proveito desses novos comportamentos.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]
3. [Adicionar um carimbo de data/hora na inserção]
4. [Atualizar o cliente para exibir o carimbo de data/hora]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior, [Introdução aos dados]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados].  

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o tamanho dos dados enviados pelos usuários. Primeiro, você registra um script que valida o tamanho dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres muito longas, neste caso mais de 10 caracteres.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo. 

   	![][0]

2. Clique na guia **Dados** e, em seguida, na tabela **TodoItem**.

   	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

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

    > [AZURE.TIP] Você pode remover um script registrado na guia **Script** clicando em **Limpar** e, em seguida, em **Salvar**.	

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1. Execute um dos seguintes arquivos de comando da subpasta **server** do projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

	+ **launch-windows** (Computadores Windows) 
	+ **launch-mac.command** (Computadores Mac OS X)
	+ **launch-linux.sh** (Computadores Linux)

	> [AZURE.NOTE] Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da Web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.

	Isso iniciará um servidor web no computador local para hospedar o aplicativo.

1. 	Abra o arquivo app.js, e substitua o manipulador de eventos **$('#add-item').submit()** pelo seguinte código:

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

2. Em um navegador da web, navegue até <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, digite texto em **Adicionar nova tarefa** e clique em **Adicionar**.

   	Observe que a operação falhará e o tratamento de erros exibirá a resposta de erro em uma caixa de diálogo.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você atualizará os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

> [AZURE.NOTE] A propriedade de carimbo de data/hora **createdAt** demonstrada aqui agora é redundante. Os Serviços Móveis criam automaticamente uma propriedade **__createdAt** do sistema para cada tabela.

1. Na guia **Scripts** no [Portal de Gerenciamento], substitua o script **Insert** atual pela função a seguir e clique em **Salvar**.

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

    > [AZURE.IMPORTANT] O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis adicionam automaticamente a coluna **createdAt** à tabela **TodoItem** na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado.

2. No navegador da web, recarregue a página e digite texto (menos de 10 caracteres) em **Adicionar nova tarefa** e clique em **Adicionar**.

   	Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3. De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.
   
   	Observe que agora há uma coluna **createdAt**, e o novo item inserido tem um valor de carimbo de data/hora.
  
Em seguida, você precisa atualizar o aplicativo para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do Serviço Móvel ignorará todos os dados em uma resposta que ele não possa serializar em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1. No editor, abra o arquivo app.js e substitua a função **refreshTodoItems** pelo seguinte código:

		function refreshTodoItems() {
			var query = todoItemTable.where(function () {
                return (this.complete === false);
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

   	Isso exibe parte da data da nova propriedade **createdAt**. 

2. No editor, abra o arquivo style.css e substitua os estilos na classe  `item-text` pelo seguinte:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	Isso redimensiona a caixa de texto e aplica o estilo ao novo texto de carimbo de data/hora.
	
6. Recarregue a página. 	

   	Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

7. Novamente na função **refreshTodoItems** substitua a linha de código que define a consulta pelo seguinte:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Essa função atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.
	
8. Recarregue a página.

   	Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação].

Para obter mais informações, consulte [Trabalhar com scripts de servidor] e [Referência conceitual ao tutorial do .NET dos Serviços Móveis]


<!-- Anchors. -->
[Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
[Adicionar um carimbo de data/hora na inserção]: #add-timestamp
[Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Trabalhar com scripts de servidor]: /pt-br/develop/mobile/how-to-guides/work-with-server-scripts
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-html
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-html
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
