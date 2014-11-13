<properties pageTitle="Autoriza&ccedil;&atilde;o no lado do atendimento (Android) | Mobile Dev Center" metaKeywords="" description="Saiba como autorizar usu&aacute;rios no back-end .NET dos Servi&ccedil;os M&oacute;veis do Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Autoriza&ccedil;&atilde;o do lado do servi&ccedil;o para usu&aacute;rios dos Servi&ccedil;os M&oacute;veis" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# Autorização do lado do serviço para usuários dos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

Este tópico mostra como autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure por meio de um aplicativo Android. Neste tutorial, você adiciona código aos métodos de acesso a dados em seu controlador que filtra consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação][Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação][Introdução à autenticação].

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## Testar o aplicativo

1.  No Eclipse, abra o projeto que você modificou quando concluiu o tutorial [Introdução à autenticação][Introdução à autenticação].

2.  No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador Android.

    Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3.  No aplicativo, insira o texto em **Adicionar um item ToDo** e clique em **Salvar**.

    ![][0]

    Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

4.  Na tabela **todoitem** no [Portal de Gerenciamento] [Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

5.  (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do Android para os Serviços Móveis][Referência conceitual do tutorial do Android para os Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com Android.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [0]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Referência conceitual do tutorial do Android para os Serviços Móveis]: /pt-br/documentation/articles/mobile-services-android-how-to-use-client-library/
