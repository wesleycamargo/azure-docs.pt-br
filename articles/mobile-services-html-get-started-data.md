<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar os dados de um aplicativo HTML. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

> [WACOM.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados de um aplicativo HTML. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo HTML][Baixar o projeto do aplicativo HTML]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

### Requisitos adicionais

Você pode hospedar o aplicativo GetStartedWithData em qualquer servidor web. No entanto, para sua conveniência, foram fornecidos scripts para permitir que você execute o aplicativo em `http://localhost:8000`.

-   Para usar o localhost, este tutorial requer que você tenha um dos seguintes servidores web em execução no seu computador local:

    -   **No Windows**: IIS Express. O IIS Express é instalado pelo [Microsoft Web Platform Installer].
    -   **No MacOS X**: Python, que já deve estar instalado.
    -   **No Linux**: Python. Você deve instalar a [versão mais recente do Python].

    Você pode usar qualquer servidor web para hospedar o aplicativo, mas estes são os servidores web com suporte dos scripts baixados.

-   Um navegador da web que ofereça suporte a HTML5.

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo HTML5. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1.  [Baixe os arquivos de projeto do aplicativo HTML][aplicativo GetStartedWithData].

2.  Em um editor HTML, abra o projeto baixado e examine o arquivo app.js.

    Observe que os itens adicionados são armazenados em um objeto **Array** na memória (**staticItems**). Quando você atualizar a página, os dados desaparecerão. Eles não são persistidos.

3.  Inicie um dos arquivos de comando a seguir da subpasta **server**.

    -   **launch-windows** (computadores com Windows)
    -   **launch-mac.command** (computadores com Mac OS X)
    -   **launch-linux.sh** (computadores com Linux)

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que voc&ecirc; confirme se deseja executar o script. Seu navegador da web poder&aacute; avis&aacute;-lo para n&atilde;o executar o script por ele ter sido baixado da internet. Quando isso acontecer, voc&ecirc; dever&aacute; solicitar que o navegador continue para carregar o script.</p>
</div>

    Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

4.  Abra a URL <http://localhost:8000/> em um navegador da web para iniciar o aplicativo.

5.  No aplicativo, digite um texto significativo, como *Concluir o tutorial*, em **Inserir nova tarefa** e clique em **Adicionar**.

    ![][0]

    Observe que o texto salvo é adicionado à matriz **staticItems** e a página é atualizada para exibir o novo item.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Clique na guia **Dados** e, em seguida, clique em **+Criar**.

    ![][1]

    Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3.  No **Nome da tabela**, digite *TodoItem* e, em seguida, clique no botão de verificação.

    ![][2]

    Isso cria a nova tabela de armazenamento **TodoItem** com as permissões padrão definidas. Isso significa que qualquer pessoa com a chave de aplicativo, que é distribuída com seu aplicativo, pode acessar e alterar dados na tabela.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>O mesmo nome de tabela &eacute; usado no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. No entanto, cada tabela &eacute; criada em um esquema espec&iacute;fico para um determinado servi&ccedil;o m&oacute;vel. Isso &eacute; para evitar colis&otilde;es de dados quando v&aacute;rios servi&ccedil;os m&oacute;veis usam o mesmo banco de dados.</p> 
</div>

4.  Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5.  Clique na guia **Colunas**. Verifique se as seguintes colunas padrão são criadas automaticamente para você:

    | Nome da coluna | Tipo                         | Índice                             |
    |----------------|------------------------------|------------------------------------|
    | ID             | string                       | Indexada                           |
    | \_\_createdAt  | data                         | Indexada                           |
    | \_\_updatedAt  | data                         | <font color="transparent">-</font> |
    | \_\_version    | carimbo de data/hora (MSSQL) | <font color="transparent">-</font> |

    Este é o requisito mínimo para uma tabela nos Serviços Móveis.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando o esquema din&acirc;mico estiver ativado no seu servi&ccedil;o m&oacute;vel, novas colunas ser&atilde;o criadas automaticamente quando objetos JSON forem enviados para o servi&ccedil;o m&oacute;vel por uma opera&ccedil;&atilde;o de inser&ccedil;&atilde;o ou atualiza&ccedil;&atilde;o.</p>
</div>

6.  Na guia **Configurar**, verifique se `localhost` já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite `localhost` no campo **Nome do host** e clique em **Salvar**.

    ![][3]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Se voc&ecirc; implantar o aplicativo quickstart em um servidor web que n&atilde;o seja o localhost, adicione o nome do host do servidor web &agrave; lista <strong>Permitir solicita&ccedil;&otilde;es de nomes de host</strong>. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx" target="_blank">Compartilhamento de recursos entre origens</a>.</p>
</div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][4]

    Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

3.  No seu editor da web, abra o arquivo de projeto index.html e adicione o seguinte às referências de script da página:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  No editor, abra o arquivo app.js, remova o comentário do código a seguir que define a variável **MobileServiceClient** e forneça a URL e a chave do aplicativo do serviço móvel no construtor **MobileServiceClient**, nessa ordem:

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel.

5.  Remova os comentários das linhas de código a seguir:

        var itemCount = 0;
        var staticItems = [];

    Os dados serão armazenados no serviço móvel, e não em uma matriz na memória.

6.  Remova os comentários da linha de código a seguir:

        todoItemTable = client.getTable('todoitem');

    Esse código criará um objeto proxy (**todoItemTable**) para o Banco de Dados SQL **TodoItem**.

7.  Substitua o manipulador de eventos **$('\#add-item').submit** pelo seguinte código:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems);
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

    Esse código insere um novo item na tabela.

8.  Substitua o método **RefreshTodoItems** pelo código a seguir:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}

    <p>Isso enviar&aacute; uma consulta para o servi&ccedil;o m&oacute;vel que retornar&aacute; todos os itens. Os resultados s&atilde;o iterados, e os dados s&atilde;o exibidos na p&aacute;gina.</p>

9.  Substitua os manipuladores de eventos <strong>$(document.body).on('change', '.item-text')</strong> e <strong>$(document.body).on('change', '.item-complete')</strong> pelo seguinte c&oacute;digo:
<pre><code>$(document.body).on(&#39;change&#39;, &#39;.item-text&#39;, function() {
    var newText = $(this).val();
    todoItemTable.update({ id: getTodoItemId(this), text: newText });
});

    $(document.body).on(&#39;change&#39;, &#39;.item-complete&#39;, function() {
    var isComplete = $(this).prop(&#39;checked&#39;);
    todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
        .then(refreshTodoItems);
});</code></pre>

   <p>Isso enviar&aacute; uma atualiza&ccedil;&atilde;o do item para o servi&ccedil;o m&oacute;vel quando o texto for alterado ou quando a caixa for selecionada.</p></li>
<li><p>Substitua o manipulador de eventos <strong>$(document.body).on('click', '.item-delete')</strong> pelo seguinte c&oacute;digo:</p>
<pre><code>$(document.body).on(&#39;click&#39;, &#39;.item-delete&#39;, function () {
    todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
});</code></pre>
<p>Ele enviar&aacute; uma exclus&atilde;o para o servi&ccedil;o m&oacute;vel quando o bot&atilde;o <strong>Excluir</strong> for clicado.</p></li>
</ol>
<p>Agora que o aplicativo foi atualizado para usar os Servi&ccedil;os M&oacute;veis para o armazenamento de back-end, &eacute; hora de testar o aplicativo com os Servi&ccedil;os M&oacute;veis.</p>
<h2><a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo servi&ccedil;o m&oacute;vel</h2>
<ol>
<li><p>Recarregue a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da web para iniciar o aplicativo.</p>
<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>

	   
  <p>Se voc&ecirc; precisar reiniciar o servidor da web, repita as etapas da primeira se&ccedil;&atilde;o.</p>

2.  Da mesma forma que antes, digite texto em **Inserir nova tarefa** e, em seguida, clique em **Adicionar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][5]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5.  No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**.

    Observe que o valor completo foi alterado de **false** para **true**.

6.  No arquivo de projeto app.js, localize o método **RefreshTodoItems** e substitua a linha do código que define `query` pelo seguinte:

        var query = todoItemTable.where({ complete: false });

7.  Carregue a página novamente e verifique um outro item da lista.

    Observe que o item marcado agora desaparecerá da lista. Cada atualização resultará em uma viagem de ida e volta para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas de como habilitar um aplicativo HTML para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    
	Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    
	Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Após concluir a série de dados, saiba como autenticar os usuários do seu aplicativo. Experimente um destes outros tutoriais após concluir [Introdução à autenticação][Introdução à autenticação].

 
 


  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-html
  [Baixar o projeto do aplicativo HTML]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345
  [0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html
