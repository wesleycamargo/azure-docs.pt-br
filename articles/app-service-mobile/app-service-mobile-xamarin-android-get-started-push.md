<properties
	pageTitle="Adicionar notificações por push a seu aplicativo Xamarin.Android com o Serviço de Aplicativo do Azure"
	description="Saiba como usar o Serviço de Aplicativo do Azure e os Hubs de notificação do Azure para enviar notificações por push para seu aplicativo Android.Xamarin"
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/02/2015" 
	ms.author="glenga"/>

# Adicionar notificações por push ao aplicativo Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Xamarin.iOS] para que sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial tem base no tutorial de [início rápido do Xamarin.Android] que você deve concluir primeiro. Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Pré-requisitos

Este tutorial exige o seguinte:

+ Uma conta ativa do Google. Você pode se inscrever em uma conta do Google em [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
   
+ [Componente do cliente Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/). Você adicionará esse componente durante o tutorial.

+ Tutorial de [início rápido do Xamarin.Android] concluído.


##<a name="create-hub"></a>Criar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [Habilitar o GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configurar seu back-end de Aplicativo Móvel para enviar solicitações por push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>Atualizar o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurar o projeto para notificações por push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testar notificações por push no seu aplicativo

Você pode testar o aplicativo usando um dispositivo virtual no emulador. Há etapas de configuração adicionais exigidas durante a execução em um emulador.

1. Verifique se você está implantando ou depurando em um dispositivo virtual com APIs do Google definidas como destino, conforme mostrado abaixo no Gerenciador de AVD (dispositivo virtual Android). 

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicione uma conta do Google ao dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta**, siga os prompts para usar Adicionar uma conta existente do Google ao dispositivo para criar uma nova.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[início rápido do Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[início rápido do Xamarin.iOS]: app-service-mobile-xamarin-android-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_1203_2015--->