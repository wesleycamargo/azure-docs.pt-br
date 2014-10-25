<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo Android dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **top** e **skip** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para evitar estouro de dados em clientes de dispositivos m&oacute;veis, os Servi&ccedil;os M&oacute;veis implementam um limite autom&aacute;tico de p&aacute;gina que tem como padr&atilde;o um m&aacute;ximo de 50 itens em uma resposta. Ao especificar o tamanho da p&aacute;gina, voc&ecirc; poder&aacute; explicitamente solicitar at&eacute; 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

1.  No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos dados][Introdução a dados].

2.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo, digite texto na caixa de texto e clique no botão **Adicionar**.

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  No arquivo ToDoActivity.java, substitua o método **RefreshTodoItems** pelo seguinte código:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5.  Recrie e inicie o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler].

    Observe que o método `top(3)` foi convertido na opção de consulta `$top=3` no URI da consulta.

7.  Atualize o método **RefreshTodoItems** mais uma vez com o código a seguir:

        private void refreshItemsFromTable() {
            // Define a filtered query that returns the top 3 items.
            mToDoTable.where().field("complete").eq(false).skip(3).top(3)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado passando valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para os m&eacute;todos <strong>top</strong> e <strong>skip</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores. Voc&ecirc; tamb&eacute;m pode chamar o m&eacute;todo <strong>includeInlineCount</strong> para obter a contagem total dos itens dispon&iacute;veis no servidor, juntamente com os dados paginados.</p>
</div>

8.  (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel.

    Observe que o método `skip(3)` foi convertido na opção de consulta `$skip=3` no URI da consulta.

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

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
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
