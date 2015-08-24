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

###4\.0.0 (07/06/2015)

-   Alterações de protocolo interno para tornar a análise e o envio mais confiáveis.
-   O push nativo (GCM/ADM) agora também é usado nas notificações de aplicativo para que você deve configurar as credenciais por push nativo para qualquer tipo de campanha de envio.
-   Corrigir a notificação de visão geral: foram exibidas somente 10s depois de enviadas.
-   Corrigir clicando em um link dentro de um anúncio da web que possui uma URL de ação padrão.
-   Corrigir uma falha rara relacionada ao gerenciamento de armazenamento local.
-   Corrigir o gerenciamento de cadeia de caracteres de configuração dinâmica.
-   Atualizar EULA.

Para versões anteriores, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md).

##Procedimentos de atualização

Se você já tiver integrado uma versão anterior do SDK no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos; se perdeu várias versões do SDK, consulte os [Procedimentos de atualização](mobile-engagement-android-upgrade-procedure.md) completos. Por exemplo, se você migrar do 1.4.0 para 1.6.0 primeiro tem que seguir o procedimento "de 1.4.0 a 1.5.0” e depois o procedimento "de 1.5.0 a 1.6.0".

Seja qual for a versão de atualização, você precisa substituir o `mobile-engagement-VERSION.jar` pelo novo.

###De 3.0.0 a 4.0.0

#### Push nativo

O push nativo (GCM/ADM) agora também é usado nas notificações de aplicativo para que você deve configurar as credenciais por push nativo para qualquer tipo de campanha de envio.

Caso ainda não tenha feito, siga [esse procedimento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

#### AndroidManifest.xml

Integração de Reach foi modificada em ``AndroidManifest.xml``.

Substitua:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Possivelmente, agora existe uma tela de carregamento quando você clica em um anúncio (com texto/conteúdo da web) ou uma pesquisa. Você deve adicioná-lo para as campanhas funcionarem em 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### Recursos

Insira o novo arquivo `res/layout/engagement_loading.xml` em seu projeto.

<!---HONumber=August15_HO7-->