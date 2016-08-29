<properties
	pageTitle="Integração do Android SDK para Azure Mobile Engagement"
	description="Descreve como integrar o SDK do Azure Mobile Engagement em aplicativos Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Integração do Android SDK para Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

Este documento descreve todas as opções de integração e configuração disponíveis para o SDK do Azure Mobile Engagement para Android.

## Pré-requisitos

[AZURE.INCLUDE [Pré-requisitos](../../includes/mobile-engagement-android-prereqs.md)]

## Recursos avançados

### Recursos de relatório

Você pode adicionar esses recursos:

1. [Opções de relatório avançadas](mobile-engagement-android-advanced-reporting.md)
2. [Opções de relatório de localização](mobile-engagement-android-location-reporting.md)
3. [Opções de configuração avançada](mobile-engagement-android-advanced-configuration.md)

### Notificações:
[Como integrar o Reach (notificações) em seu aplicativo Android](mobile-engagement-android-integrate-engagement-reach.md)

1. GCM (Google Cloud Messaging): [como integrar o GCM com o Mobile Engagement](mobile-engagement-android-gcm-integrate.md)

2. ADM (Amazon Device Messaging): [como integrar o ADM com o Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### Implementação do plano de marca:
[Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo Android](mobile-engagement-android-use-engagement-api.md)

## Notas de versão

### 4\.2.3 (08/10/2016)

 - Nenhum outro bloqueio de WIFI.
 - Corrige um deadlock ao chamar getDeviceId antes de init (bug introduzido na versão 4.2.0).

Para todas as versões, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md).

## Procedimentos de atualização

Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, consulte os [Procedimentos de atualização](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0817_2016-->