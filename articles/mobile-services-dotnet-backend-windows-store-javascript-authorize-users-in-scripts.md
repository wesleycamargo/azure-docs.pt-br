<properties pageTitle="Autorização do lado do serviço (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como autorizar usuários no back-end do .NET dos Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Autorização do lado do serviço para usuários dos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# Autorização do lado do serviço para usuários dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como autorizar os usuários autenticados para acessar dados nos Serviços Móveis do Azure em um aplicativo da Windows Store. Neste tutorial, você adiciona código aos métodos de acesso a dados em seu controlador que filtra consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação].  

## <a name="register-scripts"></a>Modificar os métodos de acesso a dados

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## Testar o aplicativo

1. No Visual Studio, abra o projeto modificado quando você concluiu o tutorial [Introdução à autenticação].

2. Pressione a tecla F5 para executar o aplicativo e fazer o logon com seu provedor de identidade. 

   	Observe que, neste momento, embora já haja itens na tabela TodoItem em tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3. No aplicativo, insira texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][3]

   	Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

6. (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados, fechando o aplicativo (Alt + F4) e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verificar se os itens inseridos na conta anterior não são exibidos. 

## Próximas etapas

Isso conclui os tutoriais que demonstram as noções básicas de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
  
* [Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis com JavaScript e HTML.

<!-- Anchors. -->
[Registrar scripts de servidor]: #register-scripts
[Próximas etapas]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push

[Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library

