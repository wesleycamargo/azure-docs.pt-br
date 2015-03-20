<properties 
	pageTitle="Introdução ao Mobile Engagement do Azure no Windows Phone" 
	description="Saiba como usar o Mobile Engagement do Azure com Análises e Notificações por Push no Windows Phone."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Introdução ao Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados usuários de um aplicativo do Windows Phone. 
Este tutorial demonstra o cenário de simples de transmissão usando o Mobile Engagement. Nele, você cria um aplicativo do Windows Phone em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação por Push da Microsoft (MPNS). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos de destino com base em suas propriedades dos dispositivos. Certifique-se de seguir o próximo tutorial para saber como usar o Mobile Engagement para atender usuários específicos e grupos de dispositivos.


Este tutorial exige o seguinte:

+ Visual Studio 2013
+ O [SDK do Windows Phone para o Mobile Engagement]

> [AZURE.IMPORTANT] Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos do Windows Phone e para concluí-lo, você deve ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

## <a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, depois em **Mobile Engagement**, depois em **Criar**.

   	![][7]

3. No popup que é exibido, insira as seguintes informações:
 
   	![][8]

	1. **Nome do aplicativo**: você pode digitar o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
	2. **Plataforma**: Selecione a plataforma de destino para que o aplicativo (se seu aplicativo for destinado para várias plataformas, repita este tutorial para cada plataforma).
	3. **Nome do recurso do aplicativo**: Esse é o nome pelo qual este aplicativo será acessado por meio de APIs e URLs. É recomendável que você use apenas caracteres convencionais da URL: o nome gerado automaticamente deve fornecer uma base sólida. Também é recomendável anexar o nome da plataforma para evitar qualquer conflito de nome, visto que esse nome deve ser exclusivo
	4. **Local**: Selecione o data center em que esse aplicativo (e mais importante sua Coleção - veja abaixo) será hospedado.
	5. **Coleção**: Se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente, caso contrário, selecione Nova Coleção.
	6. **Nome da Coleção**: Isso representa o seu grupo de aplicativos. Ele também garantirá que todos os seus aplicativos estejam em um grupo que permitirá os cálculos agregados. É altamente recomendável que você use o nome da empresa ou departamento.


	Quando terminar, clique no botão de seleção para concluir a criação do seu aplicativo.

4. Agora clique/selecione o aplicativo que você acabou de criar na guia **Aplicativo**.
 
   	![][9]

5. Depois, clique em **Informações de Conexão** para exibir as configurações de conexão para colocar em sua integração do SDK.
 
   	![][10]

6. Finalmente, anote a **Cadeia de Conexão**, que é o que você precisará para identificar esse aplicativo no seu código do aplicativo.

   	![][11]

	>[AZURE.TIP] Você pode usar o ícone de "cópia" à direita da Cadeia de Conexão para copiá-lo para a área de transferência como uma conveniência.

## <a id="connecting-app"></a>Conectando seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [documentação do SDK do Windows Phone para o Mobile Engagement].

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

### Criar um novo projeto do Windows Phone

Você pode ignorar esta etapa se você já tiver um aplicativo e estiver familiarizado com o desenvolvimento do Windows Phone.

1. Inicie o Visual Studio e, na tela inicial, selecione **Novo Projeto...**.

   	![][12]

2. Na janela pop-up, selecione (1) `Windows Phone`e (2) selecione `aplicativo do Windows Phone`.  Também (3) Preencha o `Nome` do aplicativo e (4) `Nome da solução` , depois clique em **OK**.

   	![][13]

Agora, seu projeto está criado com o aplicativo de demonstração para o qual integraremos o Mobile Engagement.

### Inclua a biblioteca de SDK em seu projeto

Baixe e integre a biblioteca de SDK

1. Baixe o [SDK do Windows Phone para o Mobile Engagement]
2. Extraia o arquivo .tar.gz para uma pasta no seu computador.
3. Vá para `PROJETO` , depois `Gerenciar Pacotes Nuget...`
4. Na janela pop-up, clique em Configurações
5. Pressione o botão `+` para criar uma nova fonte
	![][17]

6. Clique no `...` botão na parte inferior para selecionar a fonte e navegue até a pasta lib do download do SDK extraído (consulte a etapa 2), depois pressione  `Selecionar`
	![][18]

7. O SDK será adicionado como uma fonte. Basta clicar em `OK`.
	![][19]


### Conecte seu aplicativo ao back-end do Mobile Engagement com a Cadeia de Conexão

1. O SDK do Engagement precisa de alguns recursos do Windows Phone SDK para funcionar corretamente.
Abra o arquivo WMAppManifest.xml e certifique-se de que os seguintes recursos são declarados no painel recursos:
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. Volte para o portal do Azure na página **Informações de Conexão** do aplicativo e copie a **Cadeia de Conexão**.

	![][11]

3. Cole-a no arquivo `Resources\EngagementConfiguration.xml`, entre as marcas `<connectionString>`e `</connectionString>`:

	![][21]

4. No arquivo `App.xaml.cs`:
	1. Adicione a instrução `using`

			using Microsoft.Azure.Engagement;

	2. Inicializar o SDK no método `Application_Launching`:
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3. Insira o seguinte em `Application_Activated`

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

### Enviar uma tela ao Mobile Engagement

Para iniciar o envio de dados e garantir que os usuários estão ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement. Vamos atingir isso ao subclassificar nosso `MainPage` com o `EngagementPage` que o SDK do Mobile Engagement fornece.

1. Para fazer isso, substitua a superclasse `MainPage`, que vem antes de `PhoneApplicationPage`, por EngagementPage, conforme mostrado abaixo:

	![][22]

	>[AZURE.NOTE] Não se esqueça de resolver a classe se ela aparecer sublinhada em vermelho, adicionando `using Microsoft.Azure.Engagement;` para as cláusulas `using`.

2. No arquivo `MainPage.xml`:
	1. Adicione às suas declarações de namespaces:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. Substitua o `phone:PhoneApplicationPage` no nome da marca xml por `engagement:EngagementPage`

## <a id="monitor"></a>Como verificar se o seu aplicativo está conectado ao monitoramento em tempo real

Esta seção mostra como verificar se o seu aplicativo se conecta ao back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement.

	No portal do Azure, assegure-se de estar no aplicativo que está usando para este projeto e clique no botão **Engage** na parte inferior.

	![][26]

2. Você será levado para a página de configurações do seu Portal do Engagement para seu aplicativo. A partir disso, clique na guia **Monitor** conforme mostrado abaixo.
	![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo.
	![][31]

4. De volta ao Visual Studio, inicie o aplicativo no emulador ou em um dispositivo conectado clicando no triângulo verde e em seguida, selecionando o dispositivo.

5. Se funcionou, agora você deve ver uma sessão no monitor! 
	![][33]

**Parabéns!** Você concluiu com êxito a primeira etapa deste tutorial com um aplicativo que se conecta ao back-end do Mobile Engagement, que já está enviando dados.


## <a id="integrate-push"></a>Habilitar as Notificações por Push e as Mensagens no aplicativo

O Mobile Engagement permite que você interaja e entre em contato com seus usuários com Notificações por Push e Mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado REACH no portal do Mobile Engagement.
As seções a seguir irão configurar o aplicativo para recebê-los.

### Habilitar o aplicativo para receber Notificações por Push do MPNS

Adicionar novos recursos ao arquivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

### Inicializar o SDK do REACH

1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função `Application_Launching` logo após a inicialização do agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Em `App.xaml.cs`, chame `EngagementReach.Instance.OnActivated(e);` na função`Application_Activated` logo após a inicialização do agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Você está pronto, agora, verificaremos se você fez corretamente esta integração básica.

## <a id="send"></a>Como enviar uma notificação para seu aplicativo

Agora, criaremos uma campanha simples de Notificação por Push que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** no portal do Mobile Engagement.
2. Clique em **Novo comunicado** para criar sua campanha de push.
	![][35]

3. Configurar o primeiro campo da campanha por meio das seguintes etapas:
	![][36]

	1. Nomeie sua campanha com qualquer nome que desejar.
	2. Selecione **Hora de entrega** como *Any time* para permitir que o aplicativo receba uma notificação quer o aplicativo tenha sido iniciado ou não.
	3. No texto de notificação, digite o Título que estará em negrito no envio por push.
	4. Depois digite sua mensagem.

4. Role para baixo e, na seção conteúdo selecione **Notificação somente**.
![][37]

5. Você concluiu a configuração mais básica da campanha possível, agora role para baixo novamente e crie sua campanha para salvá-la!
![][38]

6. Última etapa, Ative sua campanha.
![][39]

Você verá uma notificação no seu dispositivo, **Parabéns!**:
![][40]


<!-- URLs. -->
[SDK do Windows Phone para o Mobile Engagement]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
