<properties 
	pageTitle="Integração do SDK do Windows Phone para o Mobile Engagement do Azure" 
	description="Saiba mais sobre o conteúdo do SDK do Windows Phone para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
# Conteúdo do SDK

Este documento lista e descreve o conteúdo do arquivo SDK.

## A pasta `Root`

Essa pasta contém cópias das licenças de software e um link para a documentação online.

`documentation.html` : Contém um link para a documentação online do SDK do Engagement para o Windows Phone.

Essa pasta também contém os arquivos de licença.

## A pasta `/lib`

Esta pasta contém informações sobre onde obter o SDK do Engagement.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : Pacote do NuGet para integração do SDK.

## A pasta `/Resources`

Essa pasta contém todos os recursos que o Engagement precisa. Você também pode personalizá-los para se adequarem ao seu aplicativo.

`EngagementIconNotification.png` : O ícone de marca exibido à esquerda de uma notificação.

`EngagementIconOk.png` : O ícone "Ok" do ApplicationBar da páginas do reach.

`EngagementIconCancel.png` : O ícone "Cancelar" do ApplicationBar das páginas do reach.

`EngagementIconCloseLight.png` : O ícone "Fechar" da notificação reach do Engagement do tema claro do Windows Phone.

`EngagementIconCloseDark.png` : O ícone "Fechar" da notificação reach do Engagement do tema escuro do Windows Phone.

`EngagementConfiguration.xml` : Arquivo de configuração do Engagement, é onde você pode personalizar configurações do Engagement (cadeia de conexão do Engagement, falha de relatório...).

## A pasta `/src/agent`

Esta pasta contém o EngagementPage.

`EngagementPage.cs` : A classe base para as páginas que relatam automaticamente uma atividade para o Engagement.

## A pasta `/src/reach`

Finalmente, nessa pasta, você encontrará os padrões XAML (e seus equivalentes c#) de cada página.

Você pode usá-los como base para suas próprias páginas. Basta seguir os comentários para saber o que você precisa fazer exatamente.

### Anúncio do TextView

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### Anúncio do WebView

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Pesquisa

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Notificação

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
