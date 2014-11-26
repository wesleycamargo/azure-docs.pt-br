<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos Serviços Móveis do Azure. Neste tutorial, você usará as propriedades de consulta **Skip** e **Take** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para evitar estouro de dados em clientes de dispositivos m&oacute;veis, os Servi&ccedil;os M&oacute;veis implementam um limite autom&aacute;tico de p&aacute;gina que tem como padr&atilde;o um m&aacute;ximo de 50 itens em uma resposta. Ao especificar o tamanho da p&aacute;gina, voc&ecirc; poder&aacute; explicitamente solicitar at&eacute; 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

1.  No Xamarin Studio, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados][Introdução a dados].

2.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo, insira texto na caixa de texto e clique no ícone de sinal de adição (**+**).

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  Abra o arquivo **TodoService.cs**, localize o método **RefreshDataAsync** e substitua a consulta LINQ em `todoTable` pela seguinte consulta:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5.  Recrie e inicie o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  Atualize a consulta LINQ no método **RefreshDataAsync** mais uma vez para o seguinte:

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Skip(3)
                            .Take(3)
                            .ToListAsync();

    Neste momento, defina o valor de **Skip** para 3.

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado, definindo valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para as propriedades <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores. Voc&ecirc; tamb&eacute;m pode chamar o m&eacute;todo <strong>IncludeTotalCount</strong> para obter a contagem total de todos os itens dispon&iacute;veis no servidor, junto com os dados paginados.</p>
</div>

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Se desejar obter mais informações sobre o seguinte tópico de Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. -->





  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
