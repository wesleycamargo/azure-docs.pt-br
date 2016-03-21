<properties
	pageTitle="Como definir uma API personalizada em um serviço móvel de back-end .NET | Serviços Móveis do Azure"
	description="Como definir um ponto de extremidade de API personalizada em um serviço móvel de back-end .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>


# Como definir um ponto de extremidade de API personalizada em um serviço móvel de back-end .NET
> [AZURE.SELECTOR]
- [Back-end do JavaScript](./mobile-services-javascript-backend-define-custom-api.md)
- [Back-end do .NET](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para obter a versão equivalente deste tópico para os Aplicativos Móveis, consulte [Como definir um controlador de API personalizado](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller).

Este tópico mostra como definir um ponto de extremidade de API personalizada em um serviço móvel de back-end .NET. Uma API personalizada permite que você defina pontos de extremidade personalizados com funcionalidade de servidor, mas não mapeia para uma operação de inserção, atualização, exclusão ou leitura de banco de dados. Ao usar uma API personalizada, você tem mais controle sobre mensagens, incluindo cabeçalhos HTTP e formato de corpo.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Para obter informações sobre como invocar uma API personalizada em seu aplicativo usando a biblioteca de cliente dos Serviços Móveis, veja [Chamar uma API personalizada](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) na referência do SDK do cliente.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0309_2016-->