<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autorização do lado do serviço para usuários dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este t&oacute;pico mostra como usar scripts de servidor para autorizar usu&aacute;rios autenticados a acessar dados nos Servi&ccedil;os M&oacute;veis do Azure a partir de um aplicativo do Windows Phone 8. Neste tutorial, voc&ecirc; registra scripts nos Servi&ccedil;os M&oacute;veis para filtrar consultas com base na userId de um usu&aacute;rio autenticado, garantindo que cada usu&aacute;rio possa ver apenas seus pr&oacute;prios dados.</p>
<p>Este tutorial baseia-se no in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis e no tutorial anterior <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8">Introdu&ccedil;&atilde;o &agrave; autentica&ccedil;&atilde;o</a>. Antes de iniciar este tutorial, voc&ecirc; deve primeiro concluir o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8">Introdu&ccedil;&atilde;o &agrave; autentica&ccedil;&atilde;o</a>.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">15:00:00</span></div>

</div>

## <a name="register-scripts"></a>Registrar scripts

Como o aplicativo Guia de início rápido lê e insere dados, você precisa registrar scripts para essas operações com base na tabela TodoItem.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][0]

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
<p>O esquema din&acirc;mico deve ser habilitado na primeira vez que esse script de inser&ccedil;&atilde;o for executado. Com o esquema din&acirc;mico habilitado, os Servi&ccedil;os M&oacute;veis automaticamente adicionam a coluna <strong>userId</strong> &agrave; tabela <strong>TodoItem</strong> na primeira execu&ccedil;&atilde;o. Por padr&atilde;o, o esquema din&acirc;mico &eacute; habilitado para um novo servi&ccedil;o m&oacute;vel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Phone Store.</p>
</div>

5.  Repita as etapas 3 e 4 para substituir a operação **Ler** por esta função:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Esse script filtra os objetos TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

## Testar o aplicativo

1.  No Visual Studio 2012 Express para Windows Phone, abra o projeto que você criou quando concluiu o tutorial [Introdução à autenticação][Introdução à autenticação].

2.  Pressione a tecla F5 para executar o aplicativo e fazer o logon com seu provedor de identidade.

    Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3.  No aplicativo, insira o texto na caixa de texto e clique em **Salvar**.

    ![][3]

    Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor correto de userId, o mesmo é retornado pelo serviço móvel.

4.  Na tabela **todoitem** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]

    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução às notificações por push][Introdução às notificações por push]

    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]

    Saiba mais sobre como registrar e usar scripts de servidor.





  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
