<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Usar scripts para autorizar usuários nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

Este tópico mostra como usar scripts de servidor para autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure de um aplicativo Xamarin.Android. Neste tutorial, você registra scripts nos Serviços Móveis para filtrar consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação][Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação][Introdução à autenticação].

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
<p>O esquema din&acirc;mico deve ser habilitado na primeira vez que esse script de inser&ccedil;&atilde;o for executado. Com o esquema din&acirc;mico habilitado, os Servi&ccedil;os M&oacute;veis automaticamente adicionam a coluna <strong>userId</strong> &agrave; tabela <strong>TodoItem</strong> na primeira execu&ccedil;&atilde;o. Por padr&atilde;o, o esquema din&acirc;mico &eacute; habilitado para um novo servi&ccedil;o m&oacute;vel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.</p>
</div>

5.  Repita as etapas 3 e 4 para substituir a operação **Ler** por esta função:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Esse script filtra os objetos TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

## Testar o aplicativo

1.  No Xamarin Studio ou no Visual Studio, abra o projeto modificado quando você concluiu o tutorial [Introdução à autenticação][Introdução à autenticação].

2.  Clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

    Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3.  No aplicativo, insira texto em **Inserir um TodoItem** e clique em **Salvar**.

    Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

4.  Na tabela **todoitem** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado tem um valor de userId associado.

5.  (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]

    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução às notificações por push][Introdução às notificações por push]

    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]

    Saiba mais sobre como registrar e usar scripts de servidor.





  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
