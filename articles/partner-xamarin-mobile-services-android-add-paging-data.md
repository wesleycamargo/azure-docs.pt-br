<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados ao seu aplicativo Xamarin.Android dos Serviços Móveis do Azure. Neste tutorial, você usará com o cliente os métodos de consulta **Aceitar** e **Ignorar** para solicitar "páginas" específicas de dados.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para evitar estouro de dados em clientes de dispositivos m&oacute;veis, os Servi&ccedil;os M&oacute;veis implementam um limite autom&aacute;tico de p&aacute;gina que tem como padr&atilde;o um m&aacute;ximo de 50 itens em uma resposta. Ao especificar o tamanho da p&aacute;gina, voc&ecirc; poder&aacute; explicitamente solicitar at&eacute; 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

1.  No Xamarin Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução a dados].

2.  Clique em **Executar** para iniciar o aplicativo, digite texto na caixa de texto e clique no botão **Adicionar**.

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  No arquivo **TodoActivity.cs**, substitua a consulta LINQ no método **RefreshItemsFromTableAsync** pela seguinte consulta:

        var list = await todoTable.Where(item => item.Complete == false)
                                  .Take(3)
                                  .ToListAsync();

    Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5.  Recrie e inicie o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler].

    Observe que o método `Take(3)` foi convertido na opção de consulta `$top=3` no URI da consulta.

7.  Atualize a consulta LINQ no método **RefreshItemsFromTableAsync** método mais uma vez com a seguinte consulta:

            var list = await todoTable.Where(item => item.Complete == false)
                                      .Skip(3)
                                      .Take(3)
                                      .ToListAsync();

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado passando valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para os m&eacute;todos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores. Voc&ecirc; tamb&eacute;m pode chamar o m&eacute;todo <strong>includeTotalCount</strong> para obter a contagem total dos itens dispon&iacute;veis no servidor, juntamente com os dados paginados.</p>
</div>

8.  (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel.

    Observe que o método `Skip(3)` foi convertido na opção de consulta `$skip=3` no URI da consulta.

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

 
 


  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android
