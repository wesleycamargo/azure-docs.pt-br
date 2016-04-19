<properties
	pageTitle="Introdução ao Azure Mobile Engagement para Xamarin.Android"
	description="Saiba como usar o Azure Mobile Engagement com análises e notificações por push para aplicativos Xamarin.Android."
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para aplicativos Xamarin.Android

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e como enviar notificações por push para usuários segmentados de um aplicativo Xamarin.Android. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo Xamarin.Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging).

Este tutorial exige o seguinte:

+ [Xamarin Studio](http://xamarin.com/studio) Você também pode usar o Visual Studio com Xamarin, mas este tutorial usa o Xamarin Studio. Para obter instruções de instalação, confira [Instalar e configurar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)
+ [SDK do Xamarin do Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Configuração do Mobile Engagement para seu aplicativo Android

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push.

Criaremos um aplicativo básico com o Xamarin Studio para demonstrar a integração.

###Criar um novo projeto Xamarin.Android

1. Inicie o **Xamarin Studio** Vá para **Arquivo** -> **Novo** -> **Solução** 

    ![][1]

2. Selecione **Aplicativo Android**, verifique se a linguagem selecionada é **C#** e clique em **Avançar**.

    ![][2]

3. Preencha o **Nome do Aplicativo** e o **Identificador da Organização**. Marque **Google Play Services** e clique em **Avançar**.

    ![][3]
	
4. Atualize o **Nome do Projeto**, o **Nome da Solução** e o **Local**, se necessário, e clique em **Criar**.

    ![][4]
 
O Xamarin Studio criará o aplicativo, ao qual integraremos o Mobile Engagement.

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Clique com o botão direito do mouse na pasta **Pacotes** nas janelas Solução e selecione **Adicionar Pacotes…**

    ![][5]

2. Pesquise o **SDK do Xamarin do Microsoft Azure Mobile Engagement** e adicione-o à solução.

    ![][6]
   
3. Abra **MainActivity.cs** e adicione as seguintes instruções using:

		using Microsoft.Azure.Engagement;
		using Microsoft.Azure.Engagement.Activity;

4. No método `OnCreate`, adicione o seguinte para inicializar a conexão com o back-end do Mobile Engagement. Adicione a **ConnectionString**.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
		EngagementAgent.Init(engagementConfiguration);

###Adicionar permissões e uma declaração de serviço

1. Abra o arquivo **Manifest.xm** na pasta Propriedades. Selecione a guia Fonte para que você atualize diretamente o código-fonte XML.
 
2. Adicione estas permissões a Manifest.xml (que pode ser encontrado na pasta **Propriedades**) do projeto imediatamente antes ou depois da marca `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Adicione o seguinte entre as marcas `<application>` e `</application>` para declarar o serviço do agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

4. No código que você acabou de colar, substitua `"<Your application name>"` no rótulo. Isso é exibido no menu **Configurações**, em que os usuários podem ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" desse rótulo por exemplo.

###Enviar uma tela ao Mobile Engagement

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela ao back-end do Mobile Engagement. Para fazer isso, garanta que `MainActivity` herda de `EngagementActivity` em vez de `Activity`.

	public class MainActivity : EngagementActivity

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite a você interagir e entrar em contato com seus usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

[AZURE.INCLUDE [Habilitar as mensagens em nuvem do Google](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Habilitar mensagens no aplicativo](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Enviar notificações do portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

<!---HONumber=AcomDC_0413_2016-->