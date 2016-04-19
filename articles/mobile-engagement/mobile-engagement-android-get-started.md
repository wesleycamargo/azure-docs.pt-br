<properties
	pageTitle="Introdução ao Mobile Engagement do Azure"
	description="Aprenda a usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos Android."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="04/08/2016"
	ms.author="piyushjo;ricksal" />

# Introdução ao Mobile Engagement do Azure para Aplicativos Android

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e como enviar notificações por push para usuários segmentados de um aplicativo Android. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging).

## Pré-requisitos

A conclusão deste tutorial requer as [Ferramentas para desenvolvedores do Android](https://developer.android.com/sdk/index.html), que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.

Também é necessário baixar o [SDK do Mobile Engagement Android](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## Configuração do Mobile Engagement para seu aplicativo Android

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## Conectar o aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Integração do SDK do Mobile Engagement Android](../mobile-engagement-android-sdk-overview/).

Vamos criar um aplicativo básico com o Android Studio para demonstrar a integração.

### Criar um novo aplicativo Android

1. Inicie o **Android Studio** e, no pop-up, selecione **Iniciar um novo projeto Android Studio**.

    ![][1]

2. Forneça um nome de aplicativo e um domínio da empresa. Anote o que você estiver preenchendo, já que você utilizará isso posteriormente. Clique em **Próximo**.

    ![][2]

3. Selecione o fator forma de destino e o nível de API e clique em **Avançar**.

	>[AZURE.NOTE] O Mobile Engagement requer nível mínimo de API de 10 (Android 2.3.3).

    ![][3]

4. Selecione **Atividade em Branco** aqui, que será a única tela para esse aplicativo; então, clique em **Avançar**.

    ![][4]

5. Finalmente, mantenha os padrões como estão e clique **Concluir**.

    ![][5]

O Android Studio agora criará o aplicativo de demonstração para o qual integraremos o Mobile Engagement.

### Inclua a biblioteca de SDK em seu projeto

1. Baixe o [SDK do Mobile Engagement Android].
2. Extraia o file de arquivo para uma pasta no seu computador.
3. Identifique a biblioteca. jar para a versão atual desse SDK e copie-a para a área de transferência.

	  ![][6]

4. Navegue para a seção **Projeto** (1) e cole o .jar na pasta de bibliotecas (2).

	  ![][7]

5. Sincronize seu projeto para carregar a biblioteca.

	  ![][8]

### Conecte seu aplicativo ao back-end do Mobile Engagement com a Cadeia de Conexão

1. Copie as seguintes linhas de código para a criação da atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal). Para este aplicativo de exemplo, abra a MainActivity em src -> main -> pasta java e adicione o seguinte:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resolva as referências pressionando Alt + Enter ou adicionando as seguintes instruções de importação:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Volte para o Portal Clássico do Azure na página **Informações de Conexão** de seu aplicativo e copie a **Cadeia de Conexão**.

	  ![][9]

4. Cole-a no parâmetro `setConnectionString` para substituir o exemplo fornecido, conforme mostrado abaixo:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### Adicionar permissões e uma declaração de serviço

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

### Enviar uma tela ao Mobile Engagement

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

Vá para **MainActivity.java** e adicione o seguinte para substituir a classe base de **MainActivity** para **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

Você deve comentar (excluir) a linha a seguir para este cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se você quiser manter este quadro, consulte o cenário "Relatórios básicos" no nosso [Integração avançada do Android](mobile-engagement-android-integrate-engagement.md/#basic-reporting)

## Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite a você interagir e entrar em contato com seus usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

### Copiar recursos de SDK em seu projeto

1. Navegue de volta até o conteúdo baixado do SDK e copie a pasta **res**.

	![][10]

2. Volte para o Android Studio, selecione o diretório **principal** dos arquivos de projeto e cole-o para adicionar recursos ao projeto.

	![][11]

[AZURE.INCLUDE [Habilitar as mensagens em nuvem do Google](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Habilitar mensagens no aplicativo](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Enviar notificações do portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

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
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0413_2016-->