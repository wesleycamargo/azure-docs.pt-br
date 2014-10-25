<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Autorização do lado do serviço para usuários dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure a partir de um aplicativo iOS. Neste tutorial, você adiciona código aos métodos de acesso a dados em seu controlador que filtra consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação][Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação][Introdução à autenticação].

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend][mobile-services-filter-user-results-dotnet-backend]]

## Testar o aplicativo

1.  No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução à autenticação][Introdução à autenticação].

2.  Pressione o botão **Executar** para compilar o projeto, iniciar o aplicativo no emulador do iPhone e fazer logon com o provedor de identidade escolhido.

    Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3.  No aplicativo, insira texto em **Inserir um TodoItem** e clique em **Salvar**.

    ![][0]

    Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

4.  Na tabela **todoitem** no [Portal de Gerenciamento] [Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

5.  (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

<!--## Next steps  This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:  * [Get started with data]   <br/>Learn more about storing and querying data using Mobile Services.  * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app.    * [Mobile Services .NET How-to Conceptual Reference]   <br/>Learn more about how to use Mobile Services with .NET. --> 

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "Back-end do JavaScript"
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  [0]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
