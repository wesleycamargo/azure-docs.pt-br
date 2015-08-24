<properties 
	pageTitle="Procedimentos de atualização de aplicativos do Windows Universal" 
	description="Procedimentos de atualização do SDK do Windows Universal para o Mobile Engagement do Azure" 					
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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Procedimentos de atualização de aplicativos do Windows Universal

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

##De 2.0.0 a 3.0.0

### Recursos
Esta etapa aborda apenas os recursos personalizados. Se você personalizou os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, você precisa fazer backup dos mesmos antes de atualizar e reaplicar sua personalização em recursos atualizados.

##De 1.1.1 a 2.0.0

O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure.

> [Azure.IMPORTANT]O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.1.1 e depois aplicar o procedimento a seguir

### Pacote NuGet

Substitua **Capptain.WindowsPhone** pelo pacote Nuget **MicrosoftAzure.MobileEngagement**.

### Aplicando o Mobile Engagement

O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a esta alteração.

Você precisa desinstalar o pacote nuget do Capptain atual. Considere que todas as alterações na pasta de recursos Capptain serão removidas. Se você quiser manter esses arquivos, então faça uma cópia deles.

Depois disso, instale o novo pacote nuget do Engagement do Microsoft Azure em seu projeto. Você pode encontrá-lo diretamente no [site do nuget]. ou aqui no índice. Essa ação substitui todos os arquivos de recursos usados pelo Engagement e adiciona a nova DLL do Engagement às suas referências do projeto.

Você precisa limpar as referências do projeto, excluindo as referências de Capptain DLL. Se você não fizer isso, a versão do Capptain entrará em conflito e ocorrerão erros.

Se você personalizou os recursos do Capptain, copie o conteúdo de arquivos antigos e cole-os em novos arquivos do Engagement. Observe que os arquivos xaml e cs devem ser atualizados.

Quando essas etapas forem concluídas, você só precisará substituir as referências antigas do Capptain por novas referências do Engagement.

1. Todos os namespaces Capptain precisam ser atualizados.

	Antes da migração:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Após a migração:
	
		using Microsoft.Azure.Engagement;

2. Todas as classes Capptain que contêm "Capptain" devem conter “Engagement".

	Antes da migração:
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Após a migração:
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. Para os arquivos xaml o namespace e atributos Capptain também se alteram.

	Antes da migração:
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Após a migração:
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. Alterações na página de sobreposição
	> [AZURE.IMPORTANT]A sobreposição também será alterada. O novo namespace é `Microsoft.Azure.Engagement.Overlay`. Ele deve ser usado nos arquivos xaml e cs. Além disso, `CapptainGrid` será nomeado `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são nomeados `engagement_notification_content` e `engagement_announcement_content`.
	
	Para sobreposição:
	
		<capptain:CapptainPageOverlay
		  xmlns:capptain="using:Capptain.Overlay"
		  ...
		</capptain:CapptainPageOverlay>
	
	Torna-se:
	
		<EngagementPageOverlay
		  engagement="using:Microsoft.Azure.Engagement.Overlay"
		  ...
		</engagement:EngagementPageOverlay>

5. Para outros recursos como as imagens do Capptain e arquivos HTML, observe que eles também foram renomeados para usar “Engagement".

### Declaração do projeto

Em Package.appxmanifest `File Type Associations` foi atualizado a partir de:

 -   capptain\_reach\_content para engagement\_reach\_content
 -   capptain\_log\_file para engagement\_log\_file

### ID do aplicativo / chave do SDK

O Engagement usa uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma chave do SDK com o Mobile Engagement, você só precisa especificar uma cadeia de conexão. Você pode configurá-la em seu arquivo EngagementConfiguration.

A configuração do Engagement pode ser definida no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de conexão do aplicativo entre as marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal de Gerenciamento do Azure.

### Alteração do nome de itens

Todos os itens nomeados *capptain* foram nomeados *engagement*. Da mesma forma para *Capptain* para *Engagement*.

Exemplos de itens do Capptain usados normalmente :

-   CapptainConfiguration agora denominado EngagementConfiguration
-   CapptainAgent agora denominado EngagementAgent
-   CapptainReach agora denominado EngagementReach
-   CapptainHttpConfig agora denominado EngagementHttpConfig
-   GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.

 

<!---HONumber=August15_HO7-->