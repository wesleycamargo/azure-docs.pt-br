<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Validar e modificar dados em Serviços Móveis usando scripts de servidor

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-validate-modify-data-server-scripts" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-validate-modify-data-server-scripts" title="HTML" class="current">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo iOS para tirar proveito desses novos comportamentos.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Adicionar validação de comprimento de cadeia de caracteres][Adicionar validação de comprimento de cadeia de caracteres]
2.  [Atualizar o cliente para oferecer suporte à validação][Atualizar o cliente para oferecer suporte à validação]

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
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
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

1.  No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados][Introdução a dados].

2.  Pressione o botão **Executar** (Command + R) para compilar o projeto e iniciar o aplicativo, digite texto com mais de 10 caracteres na caixa de texto e clique no ícone de sinal de adição (**+**).

    Observe que o aplicativo gera um erro não tratado como resultado da resposta 400 (solicitação incorreta) retornada pelo serviço móvel.

3.  No arquivo QSTodoService.m, localize a seguinte linha de código no método **addItem**:

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

4.  Recrie e inicie o aplicativo.

    ![][3]

    Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.

<!--## <a name="add-timestamp"></a>Add a timestamp  The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.  1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.          function insert(item, user, request) {             if (item.text.length > 10) {                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');             } else {                 item.createdAt = new Date();                 request.execute();             }         }      This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.       <div class="dev-callout"><b>Note</b>     <p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>     </div>  2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.      Notice that the new timestamp does not appear in the app UI.  3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.         Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.    Next, you need to update the iOS app to display this new column.  ## <a name="update-client-timestamp"></a>Update the client again  The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.  1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:          public class TodoItem         {             public int Id { get; set; }                        [DataMember(Name="text")]             public string Text { get; set; }              [DataMember(Name="complete")]             public bool Complete { get; set; }                          [DataMember(Name="createdAt")]             public DateTime? CreatedAt { get; set; }         }          This new class definition includes the new timestamp property, as a nullable DateTime type.        <div class="dev-callout"><b>Note</b>     <p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>     </div>  5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:                    <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>      This displays the new **CreatedAt** property in a text box.       6. Press the **F5** key to run the app.      Notice that the timestamp is only displayed for items inserted after you updated the insert script.  7. Replace the existing **RefreshTodoItems** method with the following code:           private void RefreshTodoItems()         {              // This query filters out completed TodoItems and              // items without a timestamp.              items = todoTable                .Where(todoItem => todoItem.Complete == false                    && todoItem.CreatedAt != null)                .ToCollectionView();              ListItems.ItemsSource = items;         }      This method updates the query to also filter out items that do not have a timestamp value.      8. Press the **F5** key to run the app.      Notice that all items created without timestamp value disappear from the UI.  You have completed this working with data tutorial.-->

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

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-android-validate-modify-data-server-scripts "Android"
  [HTML]: /pt-br/documentation/articles/mobile-services-html-validate-modify-data-server-scripts "HTML"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts "Xamarin.Android"
  [Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
  [Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
