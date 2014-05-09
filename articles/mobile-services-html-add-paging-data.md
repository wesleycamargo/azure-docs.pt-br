<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Adicionar paginação aos dados (HTML5)" pageTitle="Adicionar paginação aos dados (HTML5) | Mobile Dev Center" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornada para seu aplicativo HTML dos Serviços Móveis." metaCanonical="" services="" documentationCenter="Mobile" title="Refinar as consultas dos Serviços Móveis com paginação" authors="glenga" solutions="" manager="" editor="" />




# Refinar as consultas dos Serviços Móveis com paginação
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo HTML dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observação</b>
<p>Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você pode solicitar explicitamente até 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados]. Antes de iniciar este tutorial, você deve concluir pelo menos o primeiro tutorial da série sobre como trabalhar com dados [Introdução aos dados]. 

1. Execute um dos seguintes arquivos de comando da subpasta **server** do projeto que você modificou quando concluiu o tutorial sobre [Introdução aos dados].

	+ **launch-windows** (computadores Windows) 
	+ **launch-mac.command** (computadores Mac OS X)
	+ **launch-linux.sh** (computadores Linux)

	<div class="dev-callout"><b>Observação</b>
		<p>Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.</p>
	</div>

	Isso iniciará um servidor web no computador local para hospedar o aplicativo.

1. Em um navegador da web, navegue até <a href="http://localhost:8000/" target="_blank">http://localhost:8000 /</a>, digite texto em **Adicionar nova tarefa** e clique em **Adicionar**.

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem. 

2. No arquivo app.js, substitua a definição da variável `query` no método **refreshTodoItems** pela seguinte linha de código:

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	Essa consulta, quando executada, retorna os três primeiros itens que não estiverem marcados como concluídos.

3. Volte para o navegador da web e recarregue a página.

  	Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos. 

4. (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. 

   	Observe que o método **take(3)** foi convertido na opção de consulta **$top=3** no URI da consulta.

5. Atualize a consulta mais uma vez com o código a seguir:
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Volte para o navegador da web e recarregue a página.

   	Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observação</b>
    <p>Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores.  Você também pode chamar o método <strong>includeTotalCount</strong> para obter a contagem total dos itens disponíveis no servidor, juntamente com os dados paginados.</p>
    </div>

6. (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel. 

   	Observe que o método **skip(3)** foi convertido na opção de consulta **$skip=3** no URI da consulta.

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Em seguida, saiba como autenticar os usuários de seu aplicativo em [Introdução à autenticação]. Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript em [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-html
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html


[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client


