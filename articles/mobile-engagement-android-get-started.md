<properties 
	pageTitle="Introdução ao Azure Mobile Engagement" 
	description="Saiba como usar o Azure Mobile Engagement com Análises e Notificações por Push." 					services="mobile-engagement" 
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
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Introdução ao Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo Android. 
Este tutorial demonstra o simples cenário de transmissão usando o Mobile Engagement. Nela, você cria um aplicativo Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos de destino com base em suas propriedades de dispositivos. Certifique-se de seguir o próximo tutorial para aprender a usar o Mobile Engagement para atender a usuários e grupos específicos de dispositivos.


Este tutorial exige o seguinte:

+ O SDK do Android (pressupõe-se que você usará o Android Studio), que pode ser baixado [aqui](http://go.microsoft.com/fwlink/?LinkId=389797)
+ O [SDK do Mobile Engagement Android]

> [AZURE.IMPORTANT] Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos Android, e para concluí-lo, você deve ter uma conta do Azure ativa. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configure o Mobile Engagement para o seu aplicativo

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, depois em **Mobile Engagement** e, em seguida, **Criar**.

   	![][7]

3. No popup que é exibido, insira as seguintes informações:
 
   	![][8]

	1. **Nome do aplicativo**: você pode digitar o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
	2. **Plataforma**: Selecione a plataforma de destino para aquele aplicativo (se o seu aplicativo for destinado para várias plataformas, repita este tutorial para cada plataforma).
	3. **Nome de recurso do aplicativo**: Esse é o nome pelo qual este aplicativo poderá ser acessado por meio de APIs e URLs. É recomendável que você use apenas caracteres de URL convencionais: o nome gerado automaticamente deve fornecer uma base sólida. Também é recomendável anexar o nome da plataforma para evitar qualquer conflito de nome, pois esse nome deve ser exclusivo
	4. **Local**: Selecione o datacenter em que esse aplicativo (e, o mais importante, a sua coleção - veja abaixo) será hospedado.
	5. **Coleção**: Se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente, caso contrário, selecione a Nova Coleção.
	6. **Nome da coleção**: Isso representa o grupo de aplicativos. Ele também garantirá que todos os seus aplicativos estejam em um grupo que permitirá cálculos agregados. É altamente recomendável que você use o nome da sua empresa ou departamento.


	Quando terminar, clique no botão Verificar para concluir a criação do seu aplicativo.

4. Agora clique/selecione o aplicativo que você acabou de criar na guia **Aplicativo**.
 
   	![][9]

5. Em seguida, clique em **Informações de conexão** para exibir as configurações de conexão para colocar em sua integração SDK.
 
   	![][10]

6. Por fim, anote a **Cadeia de Conexão** que é o que você precisará para identificar esse aplicativo no seu código de Aplicativo.

   	![][11]

	>[AZURE.TIP] Você pode usar o ícone de "cópia" à direita da Cadeia de Conexão para copiá-lo para a área de transferência como uma conveniência.

##<a id="connecting-app"></a>Conectando seu aplicativo no back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Documentação do SDK do Mobile Engagement Android].

Vamos criar um aplicativo básico com o Android Studio para demonstrar a integração.

###Criar um novo aplicativo Android

Você pode ignorar esta etapa se já tiver um aplicativo e estiver familiarizado com o desenvolvimento do Android.

1. Inicie o studio Android e no pop-up, selecione **Iniciar um novo projeto Android Studio**.

   	![][12]

2. Preencha o nome do aplicativo e o domínio da empresa. Anote-os, pois você precisará deles mais tarde, clique em **Avançar**.

   	![][13]

3. Agora, selecione o nível de API e o fator forma de destino e clique em **Avançar**. 

	>[AZURE.NOTE] O Mobile Engagement requer nível mínimo de API de 10 (Android 2.3.3).

   	![][14]

4. Vamos agora adicionar uma Atividade a nosso aplicativo simples que será sua única e principal tela. Certifique-se de **Atividade em branco** esteja selecionada e clique em **Avançar**.

   	![][15]

5. Na tela final do assistente, você pode deixar tudo para os fins deste tutorial e clicar em **Concluir**.

   	![][16]

O Android Studio agora criará o aplicativo de demonstração para o qual integraremos o Mobile Engagement.

###Incluir a biblioteca de SDK em seu projeto

Baixe e integre a biblioteca de SDK

1. Baixe o [SDK do Mobile Engagement Android].
2. Extraia o file de arquivo para uma pasta no seu computador.
3. Identifique a biblioteca. jar para a versão atual desse SDK (esta documentação foi preparada para a versão 3.0.0) e copie-o para a área de transferência.

	![][17]

4. Navegue para a seção Projeto (1) e cole o .jar na pasta de bibliotecas (2).

	![][18]

5. Sincronize seu projeto para carregar a biblioteca.

	![][19]


###Conectar o seu aplicativo ao back-end do Mobile Engagement com a Cadeia de conexão

1. Copie as seguintes linhas de código para a criação da Atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Volte para o portal do Azure na página **Informações de conexão** de seu aplicativo e copie a **Cadeia de conexão**.

	![][11]

3. Cole-a no parâmetro `setConnectionString` para substituir o exemplo fornecido, conforme mostrado a seguir (a AppId e Sdkkey foram ocultas abaixo).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration e EngagementAgent provavelmente serão exibidos como não resolvidos (em vermelho no código). Clique em cada uma das classes não resolvidas e pressione Alt-Enter para resolvê-las automaticamente.

	![][20]

###Adicionar permissões e declaração de serviço

1. Adicionar essas permissões ao Manifest.xml do projeto imediatamente anteriores à marca `<application>`:
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. Adicione o seguinte entre as marcas < application > e < / application > para declarar o serviço agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. No código que você acabou de colar, substitua "< o nome do aplicativo >" no rótulo. Por exemplo:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###Enviar uma tela para o Mobile Engagement

Para iniciar o envio de dados e garantir que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement. Vamos fazer isso por meio de geração de subclasses para nossa Atividade com a EngagementActivity que nosso SDK fornece.
Para isso, substitua a superclasse de MainActivity, que está antes de ActionBarActivity, por EngagementActivity, conforme mostrado abaixo:

![][22]

>[AZURE.NOTE] Não se esqueça de resolver a classe se ela aparecer em vermelho, clicando nela e pressionando Alt-Enter.

##<a id="monitor"></a>Como verificar se o seu aplicativo está conectado com monitoramento em tempo real

Esta seção mostra como se certificar de que seu aplicativo se conecta ao back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement.

	Do seu portal do Azure, certifique-se de que você esteja no aplicativo que está usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior.

	![][26]

2. Você será levado para a página de configurações em seu Portal do Engagement para o seu aplicativo. A partir daí, clique na guia **Monitor**, conforme mostrado abaixo.
	![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo.
	![][31]

4. De volta ao Android Studio, inicie seu aplicativo no monitor ou em um dispositivo conectado clicando no triângulo verde e, em seguida, selecionando o dispositivo.
	![][32]

5. Caso tenha funcionado, agora você deve ver uma sessão no monitor! 
	![][33]

**Parabéns!** Você completou com sucesso a primeira etapa deste tutorial com um aplicativo que se conecta ao back-end do Mobile Engagement, que já está enviando dados.


##<a id="integrate-push"></a>Habilitando notificações por push e mensagens no aplicativo

O Mobile Engagement permite interagir e REACH seus usuários com notificações por push e em mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado REACH no portal do Mobile Engagement.
As seções a seguir configurarão o seu aplicativo para recebê-los.

### Habilitando mensagens no aplicativo

1. Copie os recursos de mensagens no aplicativo abaixo para o Manifest.xml entre as marcas < application > e < / application >.

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
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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

2. Copie os recursos para seu projeto por meio das seguintes etapas:
	1. Navegue de volta para o conteúdo para download do seu SDK e abra a pasta 'res'.
	2. Selecione as 2 pastas e copie-as para a área de transferência.

		![][23]

	4. Volte para o Android Studio, selecione a parte 'res' de seu projeto e cole-a para adicionar recursos ao seu projeto.

		![][24]

###Especificar um ícone padrão em notificações
O código a seguir definirá o ícone padrão que será exibido com as notificações. Aqui usamos o ícone fornecido com o projeto criado pelo Android Studio. Este snippet de xml deve ser colado no seu Manifest.xml entre as marcas <application> e </application>

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###Habilitar seu aplicativo para receber notificações por push do GCM

1. Insira seus metadados gcm:sender copiando e colando o seguinte em seu arquivo Manifest.xml, entre as marcas <application> e </ application>. O valor oculto abaixo (com estrelas) é o `project number` obtido do console do Google Play.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Cole o código a seguir em seu arquivo Manifest.xml, entre as marcas <application> e < / application>. Observe que em `<category android:name="com.mycompany.mysuperapp" />` usamos o nome do pacote do projeto. Em seu próprio projeto de produção, será diferente.

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
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. Adicione o último conjunto de permissões destacadas abaixo antes da marca <application>. Novamente, usamos este nome de pacote de projeto que você precisará substituir em seu aplicativo de produção.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Permitir acesso à sua chave de API do GCM para Mobile Engagement

Para permitir que o Mobile Engagement envie notificações por Push em seu nome, você precisa conceder acesso à sua chave de API. Isso é feito configurando e inserindo sua chave no portal do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	Do seu portal do Azure, certifique-se de que você esteja no aplicativo que está usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior:

	![][26]

2. Você então estará na página de configurações em seu Portal do Engagement. A partir daí, clique na seção **Push Nativo** para inserir sua chave do GCM:
	![][27]

3. Clique no ícone de edição na frente de **chave API**, na seção Configurações do GCM, conforme mostrado abaixo:
	![][28]

4. No pop-up, cole a chave de servidor GCM obtida na seção [Habilitar Google Cloud Messaging](#register) e em seguida, clique em **OK**.

	![][29]

Já estamos prontos, agora, verificaremos se a integração básica foi feita corretamente.

> [AZURE.IMPORTANT] Certifique-se de compilar, iniciar com esse novo código, sair do aplicativo e aguardar aproximadamente 1 minuto antes de fazer o seguinte

##<a id="send"></a>Como enviar uma notificação para seu aplicativo

Agora, vamos criar uma campanha de notificação por push simples que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** em seu portal do Mobile Engagement.
	![][34]

2. Clique em **Novo comunicado** para criar sua campanha de push.
	![][35]

3. Configure o primeiro campo da campanha através das seguintes etapas:
	![][36]

	1. Nomeie sua campanha com qualquer nome que desejar.
	2. Selecione o **Tipo de entrega** como *System notification / Simple*: Esse é o tipo de notificação por push simples do Android que apresenta um título e uma pequena linha de texto.
	3. Selecione **Tempo de entrega** como *Any time* para permitir que o aplicativo receba uma notificação, informando se o aplicativo é iniciado ou não.
	4. No texto de notificação, digite o título que estará em negrito no envio por push.
	5. Em seguida, digite sua mensagem.

4. Role para baixo e, na seção conteúdo, selecione **Somente notificação**.
	![][37]

5. Você concluiu a configuração da campanha mais básica possível; agora role novamente para baixo e crie a sua campanha para salvá-la!
![][38]

6. Última etapa, ativar a sua campanha.
![][39]


<!-- URLs. -->
[SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9863935
[Documentação do SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9874682
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com

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

<!--HONumber=47-->
