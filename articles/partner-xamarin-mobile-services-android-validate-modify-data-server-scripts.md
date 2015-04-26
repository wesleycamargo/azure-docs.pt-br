<properties 
	pageTitle="Usar scripts de servidor para validar e modificar dados (Xamarin Android) | Centro de desenvolvimento móvel" 
	description="Saiba como validar e modificar dados enviados usando scripts de servidor em seu aplicativo Android Xamarin." 
	documentationCenter="xamarin" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="donnam"/>

# Validar e modificar dados em Serviços Móveis usando scripts de servidor

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo Android para tirar proveito desses novos comportamentos. O código concluído está disponível no exemplo do [aplicativo ValidateModifyData][GitHub].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]
3. [Adicionar um carimbo de data/hora na inserção]
4. [Atualizar o cliente para exibir o carimbo de data/hora]

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Para começar este tutorial, primeiro conclua a [Introdução aos dados].  

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

    > [AZURE.TIP] Você pode remover um script registrado na guia **Script** clicando em **Limpar** e depois em **Salvar**.

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa verificar se seu aplicativo está tratando respostas de erros corretamente na validação.

1. No Xamarin Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

2. No arquivo TodoActivity.cs, localize o método **AddItem** e substitua a chamada para o método CreateAndShowDialog pelo código a seguir:

    	var exDetail = ex.InnerException.InnerException as 	
			MobileServiceInvalidOperationException;
    	CreateAndShowDialog(exDetail.Message, "Error");

	Isso exibe a mensagem de erro retornada pelo serviço móvel. 

3. Clique em **Executar** para iniciar o aplicativo, digite texto com mais de 10 caracteres na caixa de texto e clique no botão **Adicionar**.

  	Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

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

    > [AZURE.IMPORTANT] O esquema dinâmico deve ser habilitado na primeira vez que esse script insert for executado. Com o esquema dinâmico habilitado, os Serviços Móveis automaticamente adicionam a coluna **createdAt** à tabela **TodoItem** na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado.

2. No menu **Executar**, clique em **Executar** para iniciar o aplicativo, digite texto (com menos de 10 caracteres) na caixa de texto e clique no botão **Adicionar**.

   	Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3. De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.
   
   	Observe que agora há uma coluna **createdAt** e o novo item inserido tem um valor de carimbo de data/hora.
  
Em seguida, você precisa atualizar o aplicativo Android para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do Serviço Móvel ignorará todos os dados em uma resposta que ele não possa serializar em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1. Adicione o seguinte código às definições de campo particular na classe **TodoItem**:

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }
  
    > [AZURE.NOTE] A anotação  `DataMember's Name` informa ao cliente para mapear a nova propriedade  `CreatedAt` no aplicativo para a coluna  `createdAt` definida na tabela TodoItem, que tem um nome diferente. Usando essa anotação, seu aplicativo pode ter nomes de propriedades em objetos que são diferentes dos nomes das colunas no Banco de Dados SQL. Sem essa anotação, ocorre um erro devido às diferenças de maiúsculas e minúsculas.

2. No método GetView, adicione o seguinte código acima do local onde o código atual que define <code>checkBox.Text</code> como <code>currentItem.Text</code>:

       	string displayDate = "missing";
       	if (currentItem.CreatedAt.HasValue)
       		displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

   	Isso cria uma sequência de caracteres de data formatada quando existir um valor de carimbo de data/hora. 

3. Localize o código  `checkBox.Text = currentItem.Text` novamente e substitua essa linha de código pelo seguinte:

		checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

	Isso acrescenta a data do carimbo de data/hora ao item para exibição.
	
4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo. 

	Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

5. Em **TodoActivity.cs**, substitua a consulta existente em **RefreshItemsFromTableAsync** pela seguinte consulta:

		var list = await todoTable.Where(item => item.Complete == false && 
										 item.CreatedAt != null)
								  .ToListAsync();

	Esse método atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.
	
6. No menu **Executar**, clique em **Executar** para iniciar o aplicativo.

  	Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.

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
[0]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330



<!--HONumber=42-->
