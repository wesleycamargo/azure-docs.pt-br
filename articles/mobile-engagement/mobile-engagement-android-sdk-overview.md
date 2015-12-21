<properties
	pageTitle="Integração do SDK do Android do Azure Mobile Engagement"
	description="Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo" />


#SDK do Android do Mobile Engagement do Azure

Comece aqui a obter todos os detalhes sobre como integrar o Azure Mobile Engagement em um aplicativo Android. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de 15 minutos](mobile-engagement-android-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-android-sdk-content.md).

##Procedimentos de integração
1. Comece aqui: [como integrar o Mobile Engagement em seu aplicativo Android](mobile-engagement-android-integrate-engagement.md)

2. Para Notificações: [como integrar o Reach (Notificações) em seu aplicativo Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. GCM (Google Cloud Messaging): [como integrar o GCM com o Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. ADM (Amazon Device Messaging): [como integrar o ADM com o Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo Android](mobile-engagement-android-use-engagement-api.md)


##Notas de versão

##4\.1.3 (09/12/2015)

- Aprimoramentos de estabilidade.

##4\.1.0 (25/08/2015)

- Lidar com o novo modelo de permissão para Android M.
- Agora é possível configurar os recursos de localização no tempo de execução em vez de usar o `AndroidManifest.xml`.
- Corrija um bug de permissão: se você usar `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` não será mais necessário.
- Aprimoramentos de estabilidade.

Para versões anteriores, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md).

##Procedimentos de atualização

Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, consulte os [Procedimentos de Atualização](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_1210_2015-->