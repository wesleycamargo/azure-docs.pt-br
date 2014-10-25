<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Autorização do lado do serviço para usuários dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar scripts de servidor para autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure a partir de um aplicativo iOS. Neste tutorial, você registra scripts nos Serviços Móveis para filtrar consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação][]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação][].

## <a name="register-scripts"></a>Registrar scripts

Como o aplicativo Guia de início rápido lê e insere dados, você precisa registrar scripts para essas operações com base na tabela TodoItem.

1.  Faça logon no [Portal de Gerenciamento do Azure][], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][]

2.  Clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][1]

3.  Clique em **Script** e selecione a operação **Inserir**.

    ![][2]

4.  Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script adiciona um valor de userId para o item que é a ID do usuário autenticado, antes que ele seja inserido na tabela TodoItem.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O esquema din&acirc;mico deve ser habilitado na primeira vez que esse script de inser&ccedil;&atilde;o for executado. Com o esquema din&acirc;mico habilitado, os Servi&ccedil;os M&oacute;veis automaticamente adicionam a coluna <strong>userId</strong> &agrave; tabela <strong>TodoItem</strong> na primeira execu&ccedil;&atilde;o. Por padr&atilde;o, o esquema din&acirc;mico &eacute; habilitado para um novo servi&ccedil;o m&oacute;vel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.</p>
</div>

5.  Repita as etapas 3 e 4 para substituir a operação **Ler** por esta função:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Esse script filtra os objetos TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

## Testar o aplicativo

1.  No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução à autenticação][].

2.  Pressione o botão **Executar** para compilar o projeto, iniciar o aplicativo no emulador do iPhone e fazer logon com o provedor de identidade escolhido.

    Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3.  No aplicativo, insira texto em **Inserir um TodoItem** e clique em **Salvar**.

    ![][3]

    Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

4.  Na tabela **todoitem** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

5.  (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][]

    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução às notificações por push][]

    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência de script de servidor dos Serviços Móveis][]

    Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "Back-end do JavaScript"
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
