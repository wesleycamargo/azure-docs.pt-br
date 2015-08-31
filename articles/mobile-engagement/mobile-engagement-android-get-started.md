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
	ms.date="05/01/2015"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para Aplicativos Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e como enviar notificações por push para usuários segmentados de um aplicativo Android. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging). Ao concluir este tutorial, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos do destino com base nas propriedades dos dispositivos deles. Siga o tutorial a seguir para aprender a usar o Mobile Engagement para endereçar usuários e grupos de dispositivos específicos.


Este tutorial exige o seguinte:

+ O SDK do Android (pressupõe-se que você usará o Android Studio), que pode ser baixado [aqui](http://go.microsoft.com/fwlink/?LinkId=389797)
+ O [SDK do Mobile Engagement do Android]

> [AZURE.IMPORTANT]Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos Android, e para concluí-lo, você deve ter uma conta do Azure ativa. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo

1. Entre no [Portal do Azure](https://manage.windowsazure.com) e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, clique em **Mobile Engagement** e, em seguida, clique em **Criar**.

    ![][7]

3. No pop-up exibido, insira as seguintes informações:

    ![][8]

    - **Nome do Aplicativo**: você pode digitar o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
    - **Plataforma**: selecione a plataforma de destino para que o aplicativo (se seu aplicativo for destinado para várias plataformas, repita este tutorial para cada plataforma).
    - **Nome do Recurso do Aplicativo**: é o nome pelo qual o aplicativo poderá ser acessado via APIs e URLs. É recomendável usar apenas caracteres de URL convencionais: o nome gerado automaticamente deve fornecer uma base sólida. Também é recomendável anexar o nome da plataforma para evitar qualquer conflito de nome, já que esse nome deve ser exclusivo.
    - **Local**: selecione o datacenter em que esse aplicativo (e, mais importante, a sua Coleção – veja a seguir) será hospedado.
    - **Coleção**: se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente; caso contrário, selecione **Nova Coleção**.
    - **Nome da Coleção**: isso representa seu grupo de aplicativos. Ele também garantirá que todos os aplicativos estejam em um grupo que permita cálculos agregados. É altamente recomendável que você use o nome da empresa ou departamento.

	Quando terminar, clique no botão de seleção para concluir a criação do seu aplicativo.

4. Agora clique/selecione o aplicativo recém-criado na guia **Aplicativo**.

    ![][9]

5. Em seguida, clique em **Informações de Conexão** para exibir as configurações de **Conexão** para colocar em sua integração do SDK.

    ![][10]

6. Por fim, anote a **Cadeia de Conexão** que será necessária para identificar o aplicativo por meio do seu código de aplicativo.

    ![][11]

	>[AZURE.TIP]Você pode usar o ícone **Copiar** à direita da **Cadeia de Conexão** para copiá-lo para a área de transferência como uma conveniência.

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Documentação do SDK do Mobile Engagement Android].

Vamos criar um aplicativo básico com o Android Studio para demonstrar a integração.

###Criar um novo aplicativo Android

Também pode ignorar essa etapa se já tiver um aplicativo e estiver familiarizado com o desenvolvimento do Android.

1. Inicie o **Android Studio** e, no pop-up, selecione **Iniciar um novo projeto Android Studio**.

    ![][12]

2. Preencha o nome do aplicativo e o domínio da empresa. Anote as informações (elas serão necessárias mais tarde) e, em seguida, clique em **Avançar**.

    ![][13]

3. Agora selecione o fator forma de destino e o nível de API e clique em **Avançar**.
	>[AZURE.NOTE]O Mobile Engagement requer nível mínimo de API de 10 (Android 2.3.3).

    ![][14]

4. Vamos agora adicionar uma Atividade ao nosso aplicativo simples, que será sua única e principal tela. Certifique-se de que **Atividade em Branco** esteja selecionada e clique em **Avançar**.

    ![][15]

5. Na tela final do assistente, você pode deixar tudo como está para os fins deste tutorial e então clicar em **Concluir**.

    ![][16]

O Android Studio agora criará o aplicativo de demonstração para o qual integraremos o Mobile Engagement.

###Inclua a biblioteca de SDK em seu projeto

Baixe e integre a biblioteca de SDK

1. Baixe o [SDK do Mobile Engagement Android].
2. Extraia o file de arquivo para uma pasta no seu computador.
3. Identifique a biblioteca. jar para a versão atual desse SDK e copie-a para a área de transferência.

	  ![][17]

4. Navegue para a seção **Projeto** (1) e cole o .jar na pasta de bibliotecas (2).

	  ![][18]

5. Sincronize seu projeto para carregar a biblioteca.

	  ![][19]


###Conecte seu aplicativo ao back-end do Mobile Engagement com a Cadeia de Conexão

1. Copie as seguintes linhas de código para a criação da atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Volte para o portal do Azure na página **Informações de Conexão** de seu aplicativo e copie a **Cadeia de Conexão**.

	  ![][11]

3. Cole-o no parâmetro `setConnectionString` para substituir o exemplo fornecido, conforme mostrado a seguir (A AppId e Sdkkey foram ocultas abaixo).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. **EngagementConfiguration** e **EngagementAgent** provavelmente serão exibidos como não resolvidos (em vermelho no código). Clique em cada uma das classes não resolvidas e pressione Alt-Enter para resolvê-las automaticamente.

	  ![][20]

###Adicionar permissões e uma declaração de serviço

1. Adicionar essas permissões ao Manifest.xml do projeto imediatamente anteriores ou posteriores à marca `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	O resultado deve ser conforme mostrado abaixo:

	 ![][21]

2. Adicione o seguinte entre as marcas `<application>` e `</application>` para declarar o serviço do agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. No código que você acabou de colar, substitua `"<Your application name>"` no rótulo. Isso é exibido no menu **Configurações**, em que os usuários podem ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" desse rótulo por exemplo.

###Enviar uma tela ao Mobile Engagement

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement. Faremos isso por meio da geração de subclasses para nossa atividade com a **EngagementActivity** que o SDK fornece. Para isso, substitua a superclasse de **MainActivity**, que está antes de **ActionBarActivity**, por **EngagementActivity**, conforme mostrado abaixo:

 ![][22]

>[AZURE.NOTE]Não se esqueça de resolver a classe se ela aparecer em vermelho clicando nela e pressionando Alt-Enter.

##<a id="monitor"></a>Verificar se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo se conecte ao back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement.

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior.

	 ![][26]

2. Você será levado para a página **Configurações** do portal do Engagement para seu aplicativo. Nessa página, clique na guia **Monitor** conforme mostrado a seguir. ![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, o que iniciará o aplicativo. ![][31]

4. No Android Studio, inicie seu aplicativo no monitor ou em um dispositivo conectado clicando no triângulo verde e, em seguida, selecionando o dispositivo. ![][32]

5. Se ele funcionou, agora você deverá ver uma sessão no monitor! ![][33]

**Parabéns!** Você completou com sucesso a primeira etapa deste tutorial com um aplicativo que se conecta ao back-end do Mobile Engagement, que já está enviando dados.


##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite a você interagir e entrar em contato com seus usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

### Habilitar mensagens no aplicativo

1. Copie os recursos de mensagens no aplicativo a seguir para o Manifest.xml entre as marcas `<application>` e `</application>`.

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

		 ![][23]

	2. Volte para o Android Studio, selecione o diretório “principal” dos seus arquivos de projeto e cole-a para adicionar recursos ao seu projeto.

		 ![][24]

###Especifique um ícone para notificações

O código a seguir define o ícone usado para ser exibido em notificações tanto do sistema quanto do aplicativo.

Embora seja opcional para notificações no aplicativo, é obrigatório para notificações do sistema. O Android rejeita as notificações do sistema com ícones inválidos.

Este trecho de XML deve ser colado no Manifest.xml entre as marcas `<application>` e `</application>`.

Certifique-se de estar usando um ícone que exista em uma das pastas **sorteáveis** (como ``engagement_close.png``). Não há suporte para as pastas **mipmap**.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

É apenas um exemplo para mostrar a sintaxe. É claro que você deve usar um ícone adequado para notificações conforme as [diretrizes de design do Android](http://developer.android.com/design/patterns/notifications.html).

Você não deve usar o ícone **iniciador**. Ele tem uma resolução diferente e normalmente está nas pastas de mipmap, para as quais não há suporte.

>[AZURE.TIP]Para garantir o uso das resoluções de ícone corretas, analise [estes exemplos](https://www.google.com/design/icons). Role para baixo até a seção **Notificação**, clique em um ícone e clique em `PNGS` para baixar o conjunto de ícones sorteáveis. Você pode ver quais pastas sorteáveis usar com qual resolução para cada versão de ícone.

###Habilitar seu aplicativo para receber notificações por push do GCM

1. Insira seus metadados gcm:sender copiando e colando o seguinte em seu arquivo Manifest.xml entre as marcas `<application>` e `</application>`. O valor oculto abaixo (com estrelas) é o `project number` obtido do console do Google Play. O \\n é intencional, assim, verifique se o número do projeto termina com ele.

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

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior:

	 ![][26]

2. Você será levado para a página **Configurações** no Portal do Engagement. Nela, clique na seção **Push Nativo** para inserir a chave do GCM: ![][27]

3. Clique no ícone **Editar** edição na frente da **Chave API**, na seção **Configurações do GCM**, conforme mostrado abaixo: ![][28]

4. No pop-up, cole a Chave de Servidor do GCM obtida na seção [Habilitar Google Cloud Messaging](#register) e, em seguida, clique em **Ok**.

	 ![][29]

Você está pronto. Agora vamos verificar se você realizou corretamente essa integração básica.

> [AZURE.IMPORTANT]Certifique-se de compilar, iniciar com esse novo código, sair do aplicativo e aguardar aproximadamente 1 minuto antes de fazer o seguinte.

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora criaremos uma campanha simples de notificação por push que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** no seu portal do Mobile Engagement. ![][34]

2. Clique em **Novo comunicado** para criar sua campanha de notificação por push. ![][35]

3. Configure o primeiro campo da campanha realizando as seguintes etapas: ![][36]

	1. Atribua qualquer nome desejado à sua campanha.
	2. Selecione o **Tipo de entrega** como *Sistema de notificação / Simples*: esse é o tipo de notificação por push do Android simples que apresenta um título e uma pequena linha de texto.
	3. Selecione a **Hora de entrega** como *Sempre* para permitir que o aplicativo receba uma notificação quer ele tenha sido iniciado ou não.
	4. No texto de notificação, digite o título, que estará em negrito no envio por push.
	5. Depois digite sua mensagem.

4. Role para baixo e, na seção **Conteúdo**, selecione **Somente notificação**. ![][37]

5. Você terminou a configuração da campanha mais básica possível, agora role novamente e crie sua campanha para salvá-la! ![][38]

6. Última etapa: ativar sua campanha. ![][39]


<!-- URLs. -->
[SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9863935
[SDK do Mobile Engagement do Android]: http://go.microsoft.com/?linkid=9863935
[Documentação do SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=August15_HO8-->