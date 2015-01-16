<properties urlDisplayName="Add paging to data (HTML5)" pageTitle="Adicionar paginação aos dados (HTML 5) | Mobile Dev Center" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornados para seu aplicativo HTML dos serviços móveis." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Refinar as consultas dos Serviços Móveis com paginação

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo HTML dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observação</b>
<p>Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você pode solicitar explicitamente até 1.000 itens na resposta.</p>
</div>

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados, [Introdução aos dados]. 

1. Execute um dos seguintes arquivos de comando da subpasta **server** do projeto que você modificou quando concluiu o tutorial sobre [Introdução aos dados].

	+ **launch-windows** (computadores com Windows) 
	+ **launch-mac.command** (computadores com Mac OS X)
	+ **launch-linux.sh** (computadores com Linux)

	<div class="dev-callout"><b>Observação</b>
		<p>Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.</p>
	</div>

	Isso iniciará um servidor web no computador local para hospedar o aplicativo.

1. Em um navegador da Web, navegue até <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, digite texto em **Adicionar nova tarefa** e clique em **Adicionar**.

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
    <p>Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores.  Você também pode chamar o método  <strong>includeTotalCount</strong> para obter a contagem total de itens disponíveis no servidor, juntamente com os dados paginados.</p>
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
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client

