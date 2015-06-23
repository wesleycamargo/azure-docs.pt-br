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


#Como integrar ADM ao Engagement

> [AZURE.IMPORTANT]Você deve seguir o procedimento de integração descrito no documento Como integrar o Engagement, antes de seguir este guia.
>
> Este documento é útil apenas se você integrou o módulo do Reach para sempre dar suporte à campanha. Para integrar campanhas do Reach em seu aplicativo, leia primeiro como integrar o Engagement Reach no Android.

##Introdução

A integração do ADM permite que seu aplicativo seja enviado mesmo quando não está em execução.

Nenhum dado de campanha é realmente enviado por meio do ADM, é apenas um sinal em segundo plano informando ao aplicativo para buscar o push do Engagement. Se o aplicativo não está em execução ao receber um push ADM, ele dispara uma conexão com os servidores do Engagement para buscar o envio por push, a conexão com o Engagement permanece ativa por cerca de um minuto no caso o usuário inicie o aplicativo em resposta ao envio.

> [AZURE.IMPORTANT]Somente dispositivos Amazon Kindle executando Android 4.0.3 ou acima têm suporte pelo Amazon Device Messaging; no entanto, você pode integrar esse código com segurança em outros dispositivos. Se o aplicativo não pode ser ativado pelo ADM, a notificação do Engagement será recebida na próxima vez que o aplicativo for iniciado.

##Inscrever-se para ADM

Se ainda não tiver feito, você deve habilitar o ADM em sua conta da Amazon.

O procedimento é detalhado em: [< https://developer.amazon.com/sdk/adm/credentials.html>].

Depois de concluir o procedimento, você obtém:

-   Credenciais OAuth (uma ID de cliente e um segredo do cliente) para o Engagement para poder enviar por push seus dispositivos.
-   Uma chave de API que deve ser integrada ao seu aplicativo.

##Integração do SDK

### Gerenciando registros de dispositivo

Cada dispositivo deve enviar um comando de registro aos servidores do ADM, caso contrário eles não poderão ser alcançados.

Se você já usa a [biblioteca de cliente do ADM], e já tiver o [ADM integrado] você pode ir diretamente para o android-sdk-adm-receive.

Se você ainda não tiver o ADM integrado, o Engagement tem uma maneira mais simples para habilitá-lo em seu aplicativo:

Edite seu arquivo `AndroidManifest.xml`:

-   Adicione o namespace Amazon, o arquivo deve começar como este:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Dentro da marca `<application/>`, adicione essa seção:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Depois de adicionar a marca da amazon, você poderá ter um erro de compilação se o Destino de Compilação do Projeto estiver abaixo da versão 2.1 do Android. Você precisa usar um destino de compilação **Android 2.1 +** (não se preocupe, você ainda pode ter um `minSdkVersion` definido como 4).
-   Integre a chave de API do ADM como um ativo seguindo [este procedimento].

Siga as instruções das próximas seções.

### Comunicar a id de registro para o serviço de envio por push do Engagement e receber notificações

Para comunicar a ID de registro do dispositivo para o serviço de Envio por Push do Engagement e receber notificações, adicione o seguinte ao seu arquivo `AndroidManifest.xml`  dentro da marca `<application/>` (mesmo que você use ADM sem Engagement):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Certifique-se de ter as seguintes permissões em seu `AndroidManifest.xml` (antes da marca `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Conceda as credenciais do Engagement OAuth

Envie suas credenciais OAuth (ID do Cliente e Segredo do Cliente) em $/#application/YOUR_APPID/native-push.

Agora você pode selecionar "Qualquer hora" durante a criação de pesquisas e anúncios de alcance.


[< https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[biblioteca de cliente do ADM]: https://developer.amazon.com/sdk/adm/setup.html
[ADM integrado]: https://developer.amazon.com/sdk/adm/integrating-app.html
[este procedimento]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

<!--HONumber=54--> 