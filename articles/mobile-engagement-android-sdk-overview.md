<properties 
	pageTitle="Integração do SDK do Android do Azure Mobile Engagement" 
	description="Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#SDK do Android para Azure Mobile Engagement v3.0.0
Comece aqui a obter todos os detalhes sobre como integrar o Azure Mobile Engagement em um aplicativo Android. Se você quiser experimentá-lo pela primeira vez, não deixe de ler o nosso [tutorial em 15 minutos](mobile-engagement-android-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-android-sdk-content.md)

##Procedimentos de integração
1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo Android](mobile-engagement-android-integrate-engagement.md)

2. Para notificações: [Como integrar o Reach (notificações) em seu aplicativo Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. GCM (Google Cloud Messaging): [Como integrar GCM ao Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. ADM (Amazon Device Messaging): [Como integrar ADM ao Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementação do plano de marca: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo Android](mobile-engagement-android-use-engagement-api.md)


##Notas de versão

###3.0.0 (17/02/2015)

-   Versão inicial do Azure Mobile Engagement
-   A configuração do appId é substituída por uma configuração de cadeia de conexão.
-   API removida para enviar e receber mensagens XMPP arbitrárias de entidades XMPP arbitrárias.
-   API removida para enviar e receber mensagens entre dispositivos.
-   Aprimoramentos de segurança.
-   Acompanhamento do Google Play e SmartAd removido.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md)

##Procedimentos de atualização

Se já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK, consulte os [procedimentos de atualização] completos(mobile-engagement-android-upgrade-procedure.md). Por exemplo, se você migrar do 1.4.0 para o 1.6.0 que você seguiu pela primeira vez, siga o procedimento "de 1.4.0 para 1.5.0" e, em seguida, o procedimento "de 1.5.0 para 1.6.0".

Seja qual for a versão de atualização, você precisa substituir todos os `mobile-engagement-VERSION.jar` pelos novos.

###De 2.4.0 a 3.0.0

A seguir, está descrito como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo com o Azure Mobile Engagement. 

>[AZURE.IMPORTANT] Capptain e Mobile Engagement não são os mesmos serviços e o procedimento abaixo realça apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores Mobile Engagement

#### Arquivo JAR

Substitua `capptain.jar` por `mobile-engagement-VERSION.jar` em sua pasta `libs`.

#### Arquivos de recurso

Cada arquivo de recursos que fornecemos (antecedidos por `capptain_`) precisa ser substituído por novos(prefixados com `engagement_`).

Se você personalizou os arquivos, precisará reaplicar sua personalização nos novos arquivos, **todos os identificadores nos arquivos de recursos também foram renomeados**.

#### ID do aplicativo

Agora o Engagement usa uma cadeia de conexão para configurar os identificadores SDK, como o identificador do aplicativo.

Você precisa usar um método `EngagementAgent.init` em sua atividade de inicializador como este:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

Remova todas as chamadas de `CapptainAgent.configure` conforme o `EngagementAgent.init` substitui esse método.

O `appId` não pode ser configurado usando `AndroidManifest.xml`.

Remova essa seção do seu `AndroidManifest.xml` se você tiver:

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

#### API Java

Cada chamada a qualquer classe Java do nosso SDK precisa ser renomeada, por exemplo,  `CapptainAgent.getInstance(this)` deve ser renomeado para  `EngagementAgent.getInstance(this)`, `extends CapptainActivity` deve ser renomeado para  `extends EngagementActivity`, etc...

Se elas tiverem sido integradas com arquivos de preferência do agente padrão, o nome de arquivo padrão é agora  `engagement.agent` e a chave é  `engagement:agent`.

Ao criar anúncios da web, o associador de Javascript é agora  `engagementReachContent`.

#### AndroidManifest.xml

Ocorreram muitas alterações, o serviço não está mais compartilhado e muitos destinatários não são mais exportáveis.

Agora, a declaração de serviço é mais simples, remova o filtro intencional e todos os metadados de dentro dele e adicione  `exportable=false`.

Além disso, tudo é renomeado para usar o Engagement.

Agora, ele deverá ficar parecido com:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

Quando você desejar habilitar logs de teste, agora os metadados foram movidos para a marca de aplicativo e renomeados:

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

Todos os outros metadados têm sido apenas renomeados, aqui está a lista completa (renomeie apenas aqueles que for usar):

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

O acompanhamento do Google Play e do SmartAd foi removido do SDK, você só precisa removê-lo sem substituição:

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

As atividades do Reach agora são declaradas conforme segue:

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Se tiver atividades Reach personalizadas, você só precisa alterar as ações intencionais para corresponder  `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou  `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Os receptores de transmissão foram renomeados e agora adicionamos  `exported=false`. Aqui está a lista completa de destinatários com a nova especificação (renomeie apenas aqueles que for usar):

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
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
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

O receptor de acompanhamento foi removido, você precisará remover esta seção:

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

Observe que a declaração da sua implementação do receptor de difusão **EngagementMessageReceiver** mudou no  `AndroidManifest.xml`. Isso ocorre porque a API envia e remove mensagens XMPP arbitrárias de entidades XMPP arbitrárias e a API envia e recebe mensagens entre dispositivos que foram removidos. Portanto, você também precisa excluir os seguintes retornos de chamada de sua implementação **EngagementMessageReceiver**:

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

exclua todas as chamadas em **EngagementAgent** para:

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

			sendXMPPMessage(android.os.Bundle msg)

#### ProGuard

A configuração ProGuard pode ser afetada por mudanças de marca, as regras agora estão assim:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}

<!--HONumber=47-->
