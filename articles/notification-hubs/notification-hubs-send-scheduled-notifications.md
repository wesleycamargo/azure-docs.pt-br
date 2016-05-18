<properties
	pageTitle="Como enviar notificações agendadas | Microsoft Azure"
	description="Este tópico descreve como usar as Notificações Agendadas com os Hubs de Notificação do Azure."
	services="notification-hubs"
	documentationCenter=".net"
	keywords="notificações por push, notificação por push, agendando notificações por push"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="wesmc"/>

# Como enviar notificações agendadas


##Visão geral

Se você tiver um cenário no qual deseja enviar uma notificação em algum momento futuro, mas não tiver um maneira fácil de despertar seu código back-end para enviar a notificação. Os Hubs de Notificação do tipo Standard oferecem suporte a um recurso que permite a programação de notificações para sete dias no futuro.

Ao enviar uma notificação, simplesmente use a classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK dos Hubs de Notificação, conforme mostra o exemplo a seguir:

	Notification notification = new AppleNotification("{"aps":{"alert":"Happy birthday!"}}");
	var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Além disso, você pode cancelar uma notificação previamente agendada usando essa notificationId:

	await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Não há limites para o número de notificações agendadas que você pode enviar.

<!---HONumber=AcomDC_0413_2016-->