<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validar dados" pageTitle="Usar scripts de servidor para validar dados (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como validar e modificar os dados enviados usando scripts de servidor em seu aplicativo Windows Phone 8." metaCanonical="" services="" documentationCenter="Mobile" title="Validar e modificar dados nos Serviços Móveis usando scripts de servidor" authors="glenga" solutions="" manager="" editor="" />




# Validar e modificar dados nos Serviços Móveis usando scripts de servidor
<div class="dev-center-tutorial-selector sublanding"> 
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone" class="current">Windows Phone</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Back-end do .NET">Back-end do .NET</a> | 
	<a href="/pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como utilizar scripts de servidor nos Serviços Móveis do Azure. Os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Neste tutorial, você irá definir e registrar scripts de servidor que validam e modificam dados. Como o comportamento de scripts do lado do servidor geralmente afeta o cliente, você também irá atualizar o aplicativo do Windows Phone 8 para tirar proveito desses novos comportamentos.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a> <span class="time">11:36</span></div>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]
3. [Adicionar um carimbo de data/hora na inserção]
4. [Atualizar o cliente para exibir o carimbo de data/hora]

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução a dados].  

## <a name="string-length-validation"></a>Adicionar validação

É sempre uma boa prática validar o comprimento dos dados enviados pelos usuários. Primeiro, você registra um script que valida o comprimento dos dados enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo. 

	![][0]

2. Clique na guia **Dados** e clique na tabela **TodoItem**.

	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Esse script verifica o comprimento da propriedade **TodoItem.text** e envia uma resposta de erro quando o comprimento excede 10 caracteres. Caso contrário, o método **execute** será chamado para concluir a inserção.

    <div class="dev-callout"> 
	<b>Observação</b> 
	<p>Você pode remover um script registrado na guia <strong>Script</strong> clicando em <strong>Limpar</strong> e, em seguida, em <strong>Salvar</strong>.</p></div>	

## <a name="update-client-validation"></a>Atualizar o cliente

Agora que o serviço móvel está validando dados e enviando respostas de erros, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação.

1. No Visual Studio 2012 Express para Windows Phone, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

2. Pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto com mais de 10 caracteres na caixa de texto e clique em **Salvar**.

   	Observe que o aplicativo gera uma **MobileServiceInvalidOperationException** não tratada como resultado da resposta 400 (solicitação incorreta) retornada pelo serviço móvel.

6. 	Abra o arquivo MainPage.xaml.cs e substitua o método **InsertTodoItem** existente pelo seguinte.

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
			// When the operation completes and Mobile Services has 
			// assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
					MessageBoxButton.OK);
            }
        }

   	Essa versão do método inclui tratamento de erro para a **MobileServiceInvalidOperationException** que exibe a resposta de erro em uma caixa de mensagem.

## <a name="add-timestamp"></a>Adicionar um carimbo de data/hora

As tarefas anteriores validaram uma inserção e a aceitaram ou rejeitaram. Agora, você irá atualizar os dados inseridos usando um script de servidor que adiciona uma propriedade de carimbo de data/hora ao objeto antes que ele seja inserido.

<div class="dev-callout"><b>Observação</b>
<p>A propriedade de carimbo de data/hora <b>createdAt</b> demonstrada aqui agora é redundante. Os Serviços Móveis criam automaticamente uma propriedade do sistema <b>__createdAt</b> para cada tabela. Você pode usar essa propriedade do sistema em seu aplicativo bastando adicionar o seguinte membro à classe TodoItem</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>



1. Na guia **Scripts** do [Portal de Gerenciamento], substitua o script **Insert** atual pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Essa função aumenta o script de inserção anterior adicionando uma nova propriedade de carimbo de data/hora **createdAt** ao objeto antes que ele seja inserido pela chamada para **request**.**execute**. 

    <div class="dev-callout"><b>Observação</b>
	<p>O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis automaticamente adicionam a coluna <strong>createdAt</strong> à tabela <strong>TodoItem</strong> na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Phone Store.</p>
    </div>

2. No Visual Studio, pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto (menor do que 10 caracteres) na caixa de texto e clique em **Salvar**.

   	Observe que o novo carimbo de data/hora não aparece na interface do usuário do aplicativo.

3. De volta ao Portal de Gerenciamento, clique na guia **Procurar** na tabela **todoitem**.
   
   	Observe que agora há uma coluna **createdAt**, e o novo item inserido tem um valor de carimbo de data/hora.
  
Em seguida, você precisa atualizar o aplicativo do Windows Phone para exibir essa nova coluna.

## <a name="update-client-timestamp"></a>Atualizar o cliente novamente

O cliente do serviço móvel irá ignorar todos os dados em uma resposta que não possam ser serializados em propriedades no tipo definido. A etapa final é atualizar o cliente para exibir esses novos dados.

1. No Visual Studio, abra o arquivo MainPage.xaml.cs e substitua a classe **TodoItem** existente pela definição a seguir:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }
	
    This new class definition includes the new timestamp property, as a nullable DateTime type.
  
    <div class="dev-callout"><b>Observação</b>
	<p>O <strong>DataMemberAttribute</strong> informa ao cliente para mapear a nova propriedade <strong>CreatedAt</strong> no aplicativo para a coluna <strong>createdAt</strong> definida na tabela TodoItem, que tem maiúsculas e minúsculas diferentes. Usando esse atributo, seu aplicativo pode ter nomes de propriedades em objetos que são diferentes dos nomes das colunas no Banco de Dados SQL. Sem esse atributo, ocorre um erro devido às diferenças de maiúsculas e minúsculas.</p>
    </div>

5. Adicione o seguinte elemento XAML imediatamente abaixo do elemento **CheckBoxComplete** no arquivo MainPage.xaml:
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Isso exibe a nova propriedade **CreatedAt** em uma caixa de texto. 
	
6. Pressione a tecla **F5** para executar o aplicativo. 

   	Observe que o carimbo de data/hora é exibido somente para itens inseridos depois que você atualizou o script de inserção.

7. Substitua o método **RefreshTodoItems** existente pelo código a seguir:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;
        }

   	Esse método atualiza a consulta para também filtrar os itens que não possuem um valor de carimbo de data/hora.
	
8. Pressione a tecla **F5** para executar o aplicativo.

   	Observe que todos os itens criados sem valor de carimbo de data/hora desaparecem da interface do usuário.

Você concluiu isso trabalhando com o tutorial de dados.

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: 

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os seguintes tutoriais: [Refinar consultas com paginação].

* [Autorizar usuários com scripts]
  <br/>Saiba como filtrar dados com base na ID de um usuário autenticado.

* [Introdução às notificações de envio] 
  <br/>Aprender a enviar uma notificação de envio muito básica para seu aplicativo.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
[Adicionar um carimbo de data/hora na inserção]: #add-timestamp
[Atualizar o cliente para exibir o carimbo de data/hora]: #update-client-timestamp
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Referência ao script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

