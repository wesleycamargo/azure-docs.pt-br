<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-xamarin-android" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (Xamarin Android) | Mobile Dev Center" metaKeywords="access and change data, Azure Mobile Services, mobile devices, Azure, mobile, Xamarin.Android" description="Learn how to validate and modify data sent using server scripts from your Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Validar e modificar dados em Serviços Móveis usando scripts de servidor

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo Android para tirar proveito desses novos comportamentos. O código concluído está disponível no exemplo do [aplicativo ValidateModifyData][aplicativo ValidateModifyData].

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

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa verificar se seu aplicativo está tratando respostas de erros corretamente na validação.

1.  No Xamarin Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução a dados].

2.  No arquivo TodoActivity.cs, localize o método **AddItem** e substitua a chamada para o método CreateAndShowDialog pelo código a seguir:

        var exDetail = ex.InnerException.InnerException as  
            MobileServiceInvalidOperationException;
        CreateAndShowDialog(exDetail.Message, "Error");

    Isso exibe a mensagem de erro retornada pelo serviço móvel.

3.  Clique em **Executar** para iniciar o aplicativo, digite texto com mais de 10 caracteres na caixa de texto e clique no botão **Adicionar**.

    Observe que o erro é tratado e a mensagem de erro é exibida para o usuário.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você irá atualizar os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

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

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O esquema din&acirc;mico deve ser habilitado na primeira vez que esse script de inser&ccedil;&atilde;o for executado. Com o esquema din&acirc;mico habilitado, os Servi&ccedil;os M&oacute;veis automaticamente adicionam a coluna <strong>createdAt</strong> &agrave; tabela <strong>TodoItem</strong> na primeira execu&ccedil;&atilde;o. Por padr&atilde;o, o esquema din&acirc;mico &eacute; habilitado para um novo servi&ccedil;o m&oacute;vel e deve ser desabilitado antes que o aplicativo seja publicado.</p>
</div>

2.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo, digite texto (com menos de 10 caracteres) na caixa de texto e clique no botão **Adicionar**.

    Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3.  De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.

    Observe que agora há uma coluna **createdAt**, e o novo item inserido tem um valor de carimbo de data/hora.

Em seguida, você precisa atualizar o aplicativo Android para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do serviço móvel irá ignorar todos os dados em uma resposta que não possam ser serializados em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1.  Adicione o seguinte código às definições de campo particular na classe **TodoItem**:

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>A anota&ccedil;&atilde;o <code data-inline="1">DataMember's Name</code> informa ao cliente para mapear a nova propriedade <code data-inline="1">CreatedAt</code> no aplicativo para a coluna <code data-inline="1">createdAt</code> definida na tabela TodoItem, que tem um nome diferente. Usando essa anota&ccedil;&atilde;o, seu aplicativo pode ter nomes de propriedades em objetos que s&atilde;o diferentes dos nomes das colunas no Banco de Dados SQL. Sem essa anota&ccedil;&atilde;o, ocorre um erro devido &agrave;s diferen&ccedil;as de mai&uacute;sculas e min&uacute;sculas.</p>
</div>

2.  No método GetView, adicione o seguinte código imediatamente acima do código atual que define `checkBox.Text` como `currentItem.Text`:

        string displayDate = "missing";
        if (currentItem.CreatedAt.HasValue)
            displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

    Isso cria uma sequência de caracteres de data formatada quando existir um valor de carimbo de data/hora.

3.  Localize o código `checkBox.Text = currentItem.Text` novamente e substitua essa linha de código pelo seguinte:

        checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

    Isso acrescenta a data do carimbo de data/hora ao item para exibição.

4.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo.

    Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

5.  Em **TodoActivity.cs**, substitua a consulta existente em **RefreshItemsFromTableAsync** pela seguinte consulta:

        var list = await todoTable.Where(item => item.Complete == false && 
                                         item.CreatedAt != null)
                                  .ToListAsync();

    Esse método atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.

6.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo.

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
  [aplicativo ValidateModifyData]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
  [Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
  [Adicionar um carimbo de data/hora na inserção]: #add-timestamp
  [Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
