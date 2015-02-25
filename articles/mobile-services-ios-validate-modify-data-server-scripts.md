<properties 
	pageTitle="Usar scripts de servidor para validar e modificar dados (iOS) | Mobile Dev Center" 
	description="Saiba como validar e modificar dados enviados usando scripts de servidor em seu aplicativo iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Validar e modificar dados nos Serviços Móveis usando scripts de servidor

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você vai definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo iOS para tirar proveito desses novos comportamentos.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]


Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior, [Introdução aos dados]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados].  

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o tamanho dos dados enviados pelos usuários. Primeiro, você registra um script que valida o tamanho dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres muito longas, neste caso mais de 10 caracteres.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

   	![][0]

2. Clique na guia **Dados** e clique na tabela **TodoItem**.

   	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Esse script verifica o comprimento da propriedade **text** e envia uma resposta de erro quando o comprimento exceder 10 caracteres. Caso contrário, o método **execute** será chamado para concluir a inserção.

    > [AZURE.TIP] Você pode remover um script registrado na guia **Script** clicando em **Limpar** e, em seguida, em **Salvar**.

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1. No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

2. Pressione o botão **Executar** (Command + R) para compilar o projeto e iniciar o aplicativo, digite texto com mais de 10 caracteres na caixa de texto e clique no ícone de sinal de adição (**+**).

   	Observe que o aplicativo gera um erro não tratado como resultado da resposta 400 (solicitação incorreta) retornada pelo serviço móvel.

3. No arquivo QSTodoService.m, localize a seguinte linha de código no método **addItem**:

        [self logErrorIfNotNil:error];

   	Após essa linha de código, substitua o restante do bloco de conclusão pelo seguinte código:

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

   	Isso registra o erro na janela de saída e o exibe para o usuário.

4. Recrie e inicie o aplicativo.

   	![][4]

  	Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.

<!--## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você atualizará os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

1. Na guia **Scripts** no [Portal de Gerenciamento], substitua o script **Insert** atual pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Essa função aumenta o script de inserção anterior adicionando uma nova propriedade de carimbo de data/hora **createdAt** ao objeto antes que ele seja inserido pela chamada para **request**.**execute**.

    > [AZURE.IMPORTANTE] O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis automaticamente adicionam a coluna **createdAt** à tabela **TodoItem** na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado.

2. No Visual Studio, pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto (menor do que 10 caracteres) em **Inserir um TodoItem** e clique em **Salvar**.

   	Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3. De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.

   	Observe que agora há uma coluna **createdAt** e o novo item inserido tem um valor de carimbo de data/hora.

Em seguida, você precisa atualizar o aplicativo iOS para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do Serviço Móvel ignorará todos os dados em uma resposta que ele não possa serializar em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1. No Visual Studio, abra o arquivo MainPage.xaml.cs e substitua a classe **TodoItem** existente pela definição a seguir:

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    Essa nova definição de classe inclui a nova propriedade de carimbo de data/hora, como um tipo DateTime anulável.

    > [AZURE.NOTE] O **DataMemberAttribute** informa ao cliente para mapear a nova propriedade **CreatedAt** no aplicativo para a coluna **createdAt** definida na tabela TodoItem, que tem maiúsculas e minúsculas diferentes. Usando esse atributo, seu aplicativo pode ter nomes de propriedades em objetos que são diferentes dos nomes das colunas no Banco de Dados SQL. Sem esse atributo, ocorre um erro devido às diferenças de maiúsculas e minúsculas.

5. Adicione o seguinte elemento XAML imediatamente abaixo do elemento **CheckBoxComplete** no arquivo MainPage.xaml:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Isso exibe a nova propriedade **CreatedAt** em uma caixa de texto.

6. Pressione a tecla **F5** para executar o aplicativo.

   Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

7. Substitua o método **RefreshTodoItems** existente pelo código a seguir:

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	Esse método atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.

8. Pressione a tecla **F5** para executar o aplicativo.

   	Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.-->

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação].

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os tutoriais a seguir:

* [Autorizar usuários com scripts]
  <br/>Saiba como filtrar dados com base na ID de um usuário autenticado.

* [Introdução às notificações por push]
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
[Adicionar um carimbo de data/hora na inserção]: #add-timestamp
[Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-ios
[Autorizar usuários com scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Refinar consultas com paginação]: /en-us/develop/mobile/tutorials/add-paging-to-data-ios
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
\n<!--HONumber=42-->
