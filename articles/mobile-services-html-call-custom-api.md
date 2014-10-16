<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Chamar uma API personalizada a partir de um aplicativo HTML

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

Este tópico mostra como chamar uma API personalizada a partir de um aplicativo HTML. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][] ou [Introdução a dados][]. Para fazer isso, você executará as seguintes etapas:

1.  [Definir a API personalizada][]
2.  [Atualizar o aplicativo para chamar a API personalizada][]
3.  [Testar o aplicativo][]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][] ou [Introdução aos dados][Introdução a dados].

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api][]]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo </span>Atualizar o aplicativo para chamar a API personalizada

1.  Usando seu editor de texto, abra o arquivo index.html, localize o elemento **botão** chamado `buttonRefresh` e adicione o novo elemento a seguir logo após dele:

        <button id="buttonCompleteAll">Complete All</button> 

    Isso adiciona um novo botão à página.

2.  Em page.js e após a função **refreshTodoItems**, inclua o código a seguir:

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    Esse método identifica o evento **Click** do novo botão. O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros.

## <a name="test-app"></a>Testar o aplicativo

1.  Atualize seu navegador.

2.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

3.  Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.

4.  Clique no botão **Concluir Tudo**. É exibida uma caixa de diálogo de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

## Próximas etapas

Agora que você criou uma API personalizada e a chamou a partir de seu aplicativo HTML, saiba mais sobre os tópicos de Serviços Móveis a seguir:

-   [Referência de script de servidor de Serviços Móveis][]
    Saiba mais sobre como criar APIs personalizadas.

<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C\#]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /pt-br/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-get-started
  [Introdução a dados]: /pt-br/documentation/articles/mobile-services-html-get-started-data
  [Definir a API personalizada]: #define-custom-api
  [Atualizar o aplicativo para chamar a API personalizada]: #update-app
  [Testar o aplicativo]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
