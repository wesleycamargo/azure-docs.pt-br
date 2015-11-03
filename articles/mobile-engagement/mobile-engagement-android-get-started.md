<properties
	pageTitle="Introdução ao Mobile Engagement do Azure"
	description="Aprenda a usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos Android."
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
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para Aplicativos Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e como enviar notificações por push para usuários segmentados de um aplicativo Android. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging).

Este tutorial exige o seguinte:

+ O SDK do Android (pressupõe-se que você usará o Android Studio), que pode ser baixado [aqui](http://go.microsoft.com/fwlink/?LinkId=389797)
+ O [SDK do Mobile Engagement do Android]

> [AZURE.IMPORTANT]Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos Android, e para concluí-lo, você deve ter uma conta do Azure ativa. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="setup-azme"></a>Configuração do Mobile Engagement para seu aplicativo Android

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Integração do SDK do Mobile Engagement Android](../mobile-engagement-android-sdk-overview/).

Vamos criar um aplicativo básico com o Android Studio para demonstrar a integração.

###Criar um novo aplicativo Android

1. Inicie o **Android Studio** e, no pop-up, selecione **Iniciar um novo projeto Android Studio**.

    ![][1]

2. Forneça um nome de aplicativo e um domínio da empresa. Anote o que você estiver preenchendo, já que você utilizará isso posteriormente. Clique em **Próximo**.

    ![][2]

3. Selecione o fator forma de destino e o nível de API e clique em **Avançar**.
	
	>[AZURE.NOTE]O Mobile Engagement requer nível mínimo de API de 10 (Android 2.3.3).

    ![][3]

4. Selecione **Atividade em Branco** aqui, que será a única tela para esse aplicativo; então, clique em **Avançar**.

    ![][4]

5. Finalmente, mantenha os padrões como estão e clique **Concluir**.

    ![][5]

O Android Studio agora criará o aplicativo de demonstração para o qual integraremos o Mobile Engagement.

###Inclua a biblioteca de SDK em seu projeto

Baixe e integre a biblioteca de SDK

1. Baixe o [SDK do Mobile Engagement Android].
2. Extraia o file de arquivo para uma pasta no seu computador.
3. Identifique a biblioteca. jar para a versão atual desse SDK e copie-a para a área de transferência.

	  ![][6]

4. Navegue para a seção **Projeto** (1) e cole o .jar na pasta de bibliotecas (2).

	  ![][7]

5. Sincronize seu projeto para carregar a biblioteca.

	  ![][8]

###Conecte seu aplicativo ao back-end do Mobile Engagement com a Cadeia de Conexão

1. Copie as seguintes linhas de código para a criação da atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal). Para este aplicativo de exemplo, abra a MainActivity em src -> main -> pasta java e adicione o seguinte:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resolva as referências pressionando Alt + Enter ou adicionando as seguintes instruções de importação:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Volte para o portal do Azure na página **Informações de Conexão** de seu aplicativo e copie a **Cadeia de Conexão**.

	  ![][9]

4. Cole-a no parâmetro `setConnectionString` para substituir o exemplo fornecido, conforme mostrado abaixo:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Adicionar permissões e uma declaração de serviço

1. Adicionar essas permissões ao Manifest.xml do projeto imediatamente anteriores ou posteriores à marca `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Adicione o seguinte entre as marcas `<application>` e `</application>` para declarar o serviço do agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. No código que você acabou de colar, substitua `"<Your application name>"` no rótulo. Isso é exibido no menu **Configurações**, em que os usuários podem ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" desse rótulo por exemplo.

###Enviar uma tela ao Mobile Engagement

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

Vá para **MainActivity.java** e adicione o seguinte para substituir a classe base de **MainActivity** para **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

Você deve comentar (excluir) a linha a seguir para este cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se você quiser manter este quadro, consulte o cenário "Relatórios básicos" no nosso [Integração avançada do Android]

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite a você interagir e entrar em contato com seus usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

### Habilitar mensagens no aplicativo

1. Copie os recursos de mensagens no aplicativo a seguir para seu Manifest.xml entre as marcas `<application>` e `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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

2. Copie os recursos para seu projeto por meio das seguintes etapas:
	1. Navegue de volta para o conteúdo de download do SDK e copie a pasta 'res'.

		 ![][13]

	2. Volte para o Android Studio, selecione o diretório “principal” dos seus arquivos de projeto e cole-a para adicionar recursos ao seu projeto.

		 ![][14]

###Especifique um ícone para notificações

Cole o trecho XML a seguir no Manifest.xml entre as marcas `<application>` e `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Isso define o ícone que é exibido no sistema e notificações no aplicativo. Embora ele seja opcional para notificações no aplicativo, é obrigatório para notificações do sistema. O Android rejeita as notificações do sistema com ícones inválidos.

Use um ícone existente em uma das pastas **sorteáveis** (como ``engagement_close.png``). Não há suporte para a pasta **mipmap**.

>[AZURE.NOTE]Você não deve usar o ícone **iniciador**. Ele tem uma resolução diferente e normalmente está nas pastas de mipmap, para as quais não há suporte.

Para os aplicativos reais, você poderá usar um ícone adequado para notificações conforme as [diretrizes de design do Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP]Para garantir o uso das resoluções de ícone corretas, analise [estes exemplos](https://www.google.com/design/icons). Role para baixo até a seção **Notificação**, clique em um ícone e clique em `PNGS` para baixar o conjunto sorteável de ícones. Você pode ver quais pastas sorteáveis usar com qual resolução para cada versão de ícone.

##Criar um projeto do Google Cloud Messaging com uma chave de API 

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Habilitar seu aplicativo para receber notificações por push do GCM

1. Cole o conteúdo apresentado a seguir em seu Manifest.xml, entre as marcas `<application>` e `</application>` depois de substituir o `project number` obtido do console do Google Play. O \\n é intencional, assim, verifique se o número do projeto termina com ele.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Cole o código a seguir em seu arquivo Manifest.xml, entre as marcas `<application>` e `</application>`. Substitua o nome do pacote <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Adicione o último conjunto de permissões destacadas antes da marca `<application>`. Substitua `<Your package name>` pelo nome real do pacote do seu aplicativo.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Permitir acesso do Mobile Engagement à sua chave de API do GCM

Para permitir que o Mobile Engagement envie notificações por push em seu nome, é preciso conceder acesso à sua chave de API. Isso é feito configurando e inserindo sua chave no portal do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e clique no botão **Acionar** na parte inferior:

	![][15]

2. Em seguida, clique na seção **Configurações** -> **Push Nativo** para inserir a chave do GCM:
	  
	![][16]

3. Clique no ícone **Editar** na frente da **Chave API**, na seção **Configurações do GCM**, como mostrado abaixo:
	  
	![][17]

4. No menu pop-up, cole a Chave do Servidor GCM obtida antes e clique em **Ok**.

	![][18]

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora criaremos uma campanha simples de notificação por push que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** em seu portal do Mobile Engagement
	 
2. Clique em **Novo comunicado** para criar sua campanha de notificação por push.
	 
	![][20]

3. Configure o primeiro campo da campanha executando as seguintes etapas:
	 
	![][21]

	a. Nome de sua campanha.

	b. Selecione o **Tipo de entrega** como *Sistema de notificação -> Simples*: esse é o tipo de notificação por push do Android simples que apresenta um título e uma pequena linha de texto.

	c. Selecione a **Hora de entrega** como *Sempre* para permitir que o aplicativo receba uma notificação quer ele tenha sido iniciado ou não.

	d. No texto de notificação, digite o **Título**, que estará em negrito no envio por push.

	e. Depois digite sua **Mensagem**.

4. Role para baixo e, na seção **Conteúdo**, selecione **Somente notificação**.

	![][22]

5. Você concluiu a configuração da campanha mais básica possível. Agora, role para baixo novamente e clique no botão **Criar** para salvar sua campanha.

6. Última etapa: clique em **Ativar** para ativar sua campanha e enviar notificações por push.
    
	![][24]

<!-- URLs. -->
[SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9863935
[SDK do Mobile Engagement do Android]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682
[Integração avançada do Android]: https://azure.microsoft.com/pt-BR/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=Nov15_HO1-->