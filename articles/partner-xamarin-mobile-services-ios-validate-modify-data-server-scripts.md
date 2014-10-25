<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Validar e modificar dados em Serviços Móveis usando scripts de servidor

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo iOS para tirar proveito desses novos comportamentos. O código concluído está disponível no exemplo do [aplicativo ValidateModifyData][aplicativo ValidateModifyData].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Adicionar validação de comprimento de cadeia de caracteres][Adicionar validação de comprimento de cadeia de caracteres]
2.  [Atualizar o cliente para oferecer suporte à validação][Atualizar o cliente para oferecer suporte à validação]
3.  [Adicionar um carimbo de data/hora na inserção][Adicionar um carimbo de data/hora na inserção]
4.  [Atualizar o cliente para exibir o carimbo de data/hora][Atualizar o cliente para exibir o carimbo de data/hora]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução a dados][Introdução a dados].

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o comprimento dos dados enviados pelos usuários. Primeiro, você registra um script que valida o comprimento dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][]

2.  Clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][1]

3.  Clique em **Script** e selecione a operação **Inserir**.

    ![][2]

4.  Substitua o script existente pela função a seguir e clique em **Salvar**.

    	function insert(item, user, request) {
     	if (item.text.length \> 10) {
     	request.respond(statusCodes.BAD\_REQUEST, ‘O comprimento do texto deve ser de 10 caracteres ou menos.');
     	} else {
     	request.execute();
     	}
     	}

    Esse script verifica o comprimento da propriedade **text** e envia uma resposta de erro quando o comprimento exceder 10 caracteres. Caso contrário, o método **execute** será chamado para concluir a inserção.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Voc&ecirc; pode remover um script registrado na guia <strong>Script</strong> clicando em <strong>Limpar</strong> e, em seguida, em <strong>Salvar</strong>.</p></div>

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1.  No Xamarin Studio, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados][Introdução a dados].

2.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo, digite um texto com mais de 10 caracteres na caixa de texto e clique no ícone de sinal de adição (**+**).

    Observe que o aplicativo gera um erro não tratado como resultado da resposta 400 (solicitação incorreta) retornada pelo serviço móvel.

3.  No arquivo TodoService.cs, localize o tratamento de exceções `try/catch` atual no método **InsertTodoItemAsync** e substitua `catch` por:

    	catch (Exception ex) {
     	var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
     	Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() { 
                Title = "Error", 
                Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
        }

    Isso abrirá uma janela pop-up que exibe o erro ao usuário.

4.  Localize o método **OnAdd** em **TodoListViewController.cs**. Atualize o método para se certificar de que o `index` retornado não seja `-1` como é retornado no tratamento de exceções em **InsertTodoItemAsync**. Nesse caso, não desejamos adicionar uma nova linha a `TableView`.

    	if (index != -1) {
     	TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
     	UITableViewRowAnimation.Top);
     	itemText.Text = "";
    	}

5.  Recrie e inicie o aplicativo.

    ![][3]

    Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.

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
  [aplicativo ValidateModifyData]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
  [Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
  [Adicionar um carimbo de data/hora na inserção]: #add-timestamp
  [Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
