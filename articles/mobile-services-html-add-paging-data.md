<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo HTML dos Serviços Móveis do Azure. Neste tutorial, você usará com o cliente os métodos de consulta **Aceitar** e **Ignorar** para solicitar "páginas" específicas de dados.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para evitar estouro de dados em clientes de dispositivos m&oacute;veis, os Servi&ccedil;os M&oacute;veis implementam um limite autom&aacute;tico de p&aacute;gina que tem como padr&atilde;o um m&aacute;ximo de 50 itens em uma resposta. Ao especificar o tamanho da p&aacute;gina, voc&ecirc; poder&aacute; explicitamente solicitar at&eacute; 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

1.  Execute um dos seguintes arquivos de comando da subpasta **server** do projeto que você modificou quando concluiu o tutorial sobre [Introdução aos dados][Introdução a dados].

    -   **launch-windows** (computadores com Windows)
    -   **launch-mac.command** (computadores com Mac OS X)
    -   **launch-linux.sh** (computadores com Linux)

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que voc&ecirc; confirme se deseja executar o script. Seu navegador da web poder&aacute; avis&aacute;-lo para n&atilde;o executar o script por ele ter sido baixado da internet. Quando isso acontecer, voc&ecirc; dever&aacute; solicitar que o navegador continue para carregar o script.</p>
</div>

    Isso iniciará um servidor web no computador local para hospedar o aplicativo.

2.  Em um navegador da web, navegue até [http://localhost:8000 /][], digite texto em **Adicionar nova tarefa** e clique em **Adicionar**.

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  No arquivo app.js, substitua a definição da variável `query` no método **refreshTodoItems** pela seguinte linha de código:

        var query = todoItemTable.where({ complete: false }).take(3);

    Essa consulta, quando executada, retorna os três primeiros itens que não estiverem marcados como concluídos.

5.  Volte para o navegador da web e recarregue a página.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler].

    Observe que o método **take(3)** foi convertido na opção de consulta **$top=3** no URI da consulta.

7.  Atualize a consulta mais uma vez com o código a seguir:

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Volte para o navegador da web e recarregue a página.

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado passando valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para os m&eacute;todos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores.  Voc&ecirc; tamb&eacute;m pode chamar o m&eacute;todo <strong>includeTotalCount</strong> para obter a contagem total dos itens dispon&iacute;veis no servidor, juntamente com os dados paginados.</p>
</div>

9.  (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel.

    Observe que o método **skip(3)** foi convertido na opção de consulta **$skip=3** no URI da consulta.

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Em seguida, saiba como autenticar os usuários de seu aplicativo em [Introdução à autenticação][]. Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript em [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis][]

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/develop/mobile/tutorials/add-paging-to-data-js "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /pt-br/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [Xamarin.iOS]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
  [http://localhost:8000 /]: http://localhost:8000/
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html
  [Referência conceitual do tutorial HTML/JavaScript nos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client
