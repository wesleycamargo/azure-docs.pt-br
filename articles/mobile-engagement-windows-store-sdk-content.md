<properties 
	pageTitle="Conteúdo do SDK da Windows Store para o Mobile Engagement do Azure" 
	description="Atualizações mais recentes e procedimentos para o SDK da Windows Store para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Conteúdo do SDK

Este documento lista e descreve o conteúdo do arquivo SDK.

## A pasta `Root`

Essa pasta contém cópias das licenças de software e um link para a documentação online.

`documentation.html` : Contém um link para a documentação online do SDK do Engagement para aplicativos c# Metro do Windows 8.

Essa pasta também contém os arquivos de licença.

## A pasta `/lib`

Esta pasta contém informações sobre onde obter o SDK do Engagement.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : Pacote do NuGet para integração do SDK.

## A pasta `/Resources`

Essa pasta contém todos os recursos que o Engagement precisa. Você também pode personalizá-los para se adequarem ao seu aplicativo.

`EngagementConfiguration.xml` : Arquivo de configuração do Engagement, é onde você pode personalizar configurações do Engagement (cadeia de conexão do Engagement, falha de relatório...).

### /pasta html

`EngagementNotification.html` :  O design html do modo de exibição da web  `Notificação`.

`EngagementAnnouncement.html` :  O design html do modo de exibição da web `Anúncio`.

### /pasta imagens

`EngagementIconNotification.png` : O ícone de marca exibido à esquerda de uma notificação.

`EngagementIconOk.png` :  O ícone `Ok` ícone das páginas de conteúdo do reach para o botão de ação ou de validação.

`EngagementIconNOK.png` :  O ícone `NOK` usado quando o botão de validação das páginas de conteúdo do reach é desabilitado.

`EngagementIconClose.png` :  O ícone `Fechar` de notificações e conteúdos do reach para o botão descartar.

### /pasta sobreposição

`EngagementOverlayAnnouncement.xaml` :  O design xaml `Anúncio`.

`EngagementOverlayAnnouncement.xaml.cs` : O código vinculado  `EngagementOverlayAnnouncement.xaml`.

`EngagementOverlayNotification.xaml` :  O design xaml `Notificação`.

`EngagementOverlayNotification.xaml.cs` : O código vinculado  `EngagementOverlayNotification.xaml`.

`EngagementPageOverlay.cs` :  O código de exibição de anúncio e notificação `Sobreposição`.

## A pasta `/src/agent`

Esta pasta contém o EngagementPage.

`EngagementPage.cs` : A classe base para as páginas que relatam automaticamente uma atividade para o Engagement.

<!--HONumber=47-->
