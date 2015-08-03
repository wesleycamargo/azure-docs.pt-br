<properties
	pageTitle="Saiba como chamar uma API personalizada de um cliente iOS"
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo iOS que usa os serviços móveis do Azure."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# Como chamar uma API personalizada de um cliente iOS (back-end JavaScript)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo iOS. Uma API personalizada permite que você defina pontos de extremidade personalizados com funcionalidade de servidor, mas não mapeia para uma operação de inserção, atualização, exclusão ou leitura de banco de dados. Ao usar uma API personalizada, você tem mais controle sobre mensagens, incluindo cabeçalhos HTTP e formato de corpo.

## <a name="define-custom-api"></a>Definir uma API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## Próximas etapas

Este tópico mostrou como usar o método **invokeApi** para chamar uma API personalizada bastante simples de seu aplicativo do iOS. Para saber mais sobre como usar o método **invokeApi**, consulte a postagem [API personalizada nos Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Além disso, considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Referência de script de servidor de Serviços Móveis]<br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte] <br/>Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[Scripts de servidor de armazenamento no controle do código-fonte]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->