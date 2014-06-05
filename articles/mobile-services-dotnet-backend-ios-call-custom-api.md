<properties pageTitle="Chamar uma API personalizada em um aplicativo iOS | Serviços Móveis" metaKeywords="" description="Saiba como definir uma API personalizada e chamá-la em um aplicativo iOS que usa os Serviços Móveis do Windows Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Chamar uma API personalizada no cliente" authors=""  solutions="" writer="krisragh" manager="" editor=""  />


# Chamar uma API personalizada no cliente 

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-call-custom-api"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como chamar uma API personalizada em um aplicativo iOS. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item de tarefas pendentes na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo] 

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. Este tutorial requer o [SDK do iOS para Serviços Móveis](https://go.microsoft.com/fwLink/p/?LinkID=266533), o [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) e o iOS 5.0 ou posterior.

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo iOS, procure mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte]
  <br/> Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Definir a API personalizada]: #define-custom-api
[Atualizar o aplicativo para chamar a API personalizada]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Notificações por push e Live Connect do Windows]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Scripts de servidor de armazenamento no controle do código-fonte]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control

