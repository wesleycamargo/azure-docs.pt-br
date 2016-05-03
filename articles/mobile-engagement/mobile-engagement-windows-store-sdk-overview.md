<properties 
	pageTitle="Visão geral do SDK do Windows Universal" 
	description="Visão geral do SDK do Windows Universal para o Mobile Engagement do Azure" 									
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
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Visão geral do SDK do Windows Universal para o Mobile Engagement do Azure

Comece aqui para obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo do Windows Universal. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-windows-store-sdk-content.md)

##Procedimentos de integração

1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo do Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Para Notificações: [Como integrar o Reach (Notificações) em seu aplicativo do Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

##Notas de versão

###3\.4.0 (19/04/2016)

-   Melhorias de sobreposição de alcance.
-   Adicionada a API "TestLogLevel" para habilitar/desabilitar/filtrar logs de console emitidos pelo SDK.
-   Corrigidas as notificações na atividade visando a primeira atividade que não eram exibidas no início do Aplicativo.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-windows-store-release-notes.md)

##Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK consulte os [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md) completos. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

###De 3.3.0 a 3.4.0

####Logs de teste

Agora, os logs do console produzidos pelo SDK podem ser habilitados/desabilitados/filtrados. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

####Recursos

A sobreposição do Alcance foi aprimorada. Ela faz parte dos recursos do pacote NuGet do SDK.

Ao atualizar para a nova versão do SDK, você pode escolher se deseja ou não manter os arquivos existentes da pasta de sobreposição de seus recursos:

* Se a sobreposição anterior estiver funcionando para você ou se você estiver integrando os elementos `WebView` manualmente, você poderá optar por manter os arquivos existentes e ela ainda funcionará. 
* Se você deseja atualizar para a nova sobreposição, basta substituir toda a pasta `overlay` dos seus recursos pela nova pasta do pacote do SDK (aplicativos UWP: após a atualização, você pode obter a nova pasta de sobreposição de %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources).

> [AZURE.WARNING] Usar a nova sobreposição substituirá todas as personalizações feitas na versão anterior.

### Atualizar de versões anteriores

Consulte [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_0420_2016-->