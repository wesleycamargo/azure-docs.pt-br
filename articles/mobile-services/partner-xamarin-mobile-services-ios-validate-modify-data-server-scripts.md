<properties
	pageTitle="Usar scripts de servidor para validar e modificar dados (Xamarin iOS) | Centro de desenvolvimento móvel"
	description="Saiba como validar e modificar dados enviados usando scripts de servidor de seu aplicativo iOS Xamarin."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# Valide e modifique os dados nos Serviços Móveis usando scripts de servidor

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo iOS para tirar proveito desses novos comportamentos. O código concluído está disponível no exemplo do [aplicativo ValidateModifyData][GitHub].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para dar suporte à validação]
3. [Adicionar um carimbo de data/hora ao inserir]
4. [Atualizar o cliente para exibir o carimbo de data/hora]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados]. Antes de começar este tutorial, primeiro conclua [Introdução a dados].

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o tamanho dos dados enviados pelos usuários. Primeiro, você registra um script que valida o comprimento dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

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

    > [AZURE.NOTE]Você pode remover um script registrado na guia **Script** clicando em **Limpar** e, em seguida, em **Salvar**.

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar das respostas de erros na validação.

1. No Xamarin Studio, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo, digite um texto com mais de 10 caracteres na caixa de texto e clique no ícone de sinal de adição (**+**).

	Observe que o aplicativo gera um erro não tratado como resultado da resposta 400 (Solicitação Incorreta) retornada pelo serviço móvel.

3. No arquivo TodoService.cs, localize o tratamento de exceções <code>try/catch</code> atual no método **InsertTodoItemAsync** e substitua o <code>catch</code> por:

    catch (Exception ex) { var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException); Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() {
            	Title = "Error",
            	Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
		}

	Isso abrirá uma janela pop-up que exibe o erro ao usuário.

4. Localize o método **OnAdd** em **TodoListViewController.cs**. Atualize o método para se certificar de que o <code>index</code> não seja <code>-1</code> como é retornado no tratamento de exceções em **InsertTodoItemAsync**. Nesse caso, não desejamos adicionar uma nova linha a <code>TableView</code>.

    if (index != -1) { TableView.InsertRows(new { NSIndexPath.FromItemSection(index, 0) }, UITableViewRowAnimation.Top); itemText.Text = ""; }


5. Recompile e inicie o aplicativo.

	![][4]

	Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.


## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados [Refinar consultas com paginação].

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os tutoriais a seguir:

* [Autorizar usuários com scripts] <br/>Saiba como filtrar dados com base na ID de um usuário autenticado.

* [Introdução às notificações por push] <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência de script de servidor dos Serviços Móveis] <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para dar suporte à validação]: #update-client-validation
[Adicionar um carimbo de data/hora ao inserir]: #add-timestamp
[Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Autorizar usuários com scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Refinar consultas com paginação]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Introdução a dados]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução aos dados]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330
 

<!---HONumber=July15_HO4-->