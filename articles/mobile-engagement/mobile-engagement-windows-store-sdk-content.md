<properties 
	pageTitle="Conteúdo do SDK de aplicativos do Windows Universal" 
	description="Saiba mais sobre o conteúdo do SDK dos aplicativos do Windows Universal para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="piyushjo" />

#Conteúdo do SDK de aplicativos do Windows Universal

Este documento lista e descreve o conteúdo implantado pelo SDK em seu aplicativo.

##A pasta `/Resources`

Essa pasta contém todos os recursos que o Engagement precisa. Você também pode personalizá-los para se adequarem ao seu aplicativo.

- `EngagementConfiguration.xml` : Arquivo de configuração do Mobile Engagement, é onde você pode personalizar configurações do Mobile Engagement (cadeia de conexão do Mobile Engagement, relatar falhas, etc.).

### /pasta html

- `EngagementNotification.html` : O design do modo de exibição web html do `Notification`.

- `EngagementAnnouncement.html` : O design do modo de exibição web html do `Announcement`.

### /pasta imagens

- `EngagementIconNotification.png` : O ícone de marca exibido à esquerda de uma notificação, substitua-o pelo ícone da sua marca.

- `EngagementIconOk.png` : O ícone `Ok` das páginas de conteúdo do reach para o botão de ação ou de validação.

- `EngagementIconNOK.png` : O ícone `NOK` usado quando o botão de validação das páginas de conteúdo do reach é desabilitado.
 
- `EngagementIconClose.png` : O ícone `Close` de notificações e conteúdos do reach para o botão descartar.

### /pasta sobreposição

- `EngagementOverlayAnnouncement.xaml` : O design do xaml do `Announcement`.

- `EngagementOverlayAnnouncement.xaml.cs` : O código vinculado `EngagementOverlayAnnouncement.xaml`.
 
- `EngagementOverlayNotification.xaml` : O design do xaml do `Notification`.
 
- `EngagementOverlayNotification.xaml.cs` : O código vinculado `EngagementOverlayNotification.xaml`.
 
- `EngagementPageOverlay.cs` : O código de exibição de anúncio e notificação `Overlay`.
  

<!---HONumber=August15_HO6-->