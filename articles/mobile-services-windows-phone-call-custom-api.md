<properties linkid="mobile-services-call-custom-api-wp8" urlDisplayName="Chamar uma API personalizada do cliente" pageTitle="Chamar uma API personalizada de um cliente Windows Store - Serviços Móveis" metaKeywords="" description="Saiba como definir uma API personalizada e, em seguida, chamá-la em um aplicativo do Windows Phone que usa os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="" title="Chamar uma API personalizada no cliente" authors="glenga" solutions="" manager="" editor="" />




# Chamar uma API personalizada no cliente

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como chamar uma API personalizada em um aplicativo do Windows Phone. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução a dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo] 

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução aos Serviços Móveis] ou [Introdução a dados]. Este tutorial usa o Visual Studio 2012 Express para Windows Phone.

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo do Windows Phone, procure mais informações sobre os seguintes tópicos de Serviços Móveis:

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
[Referência ao script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/

[Scripts de servidor de armazenamento no controle do código-fonte]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control

