<properties 
	pageTitle="Visão geral SDK da Windows Store para o Mobile Engagement do Azure" 
	description="Atualizações mais recentes e procedimentos para o SDK da Windows Store para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# 2.0.0

Comece aqui para obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo da Windows Store. Se você gostaria de experimentá-lo primeiro, faça nosso tutorial de [15 minutos](../mobile-engagement-windows-store-dotnet-get-started/).

Clique para ver o [Conteúdo do SDK](../mobile-engagement-windows-store-sdk-content/)

## Procedimentos de integração

1. Comece por aqui: [Como integrar o Mobile Engagement em seu aplicativo da Windows Store](../mobile-engagement-windows-store-integrate-engagement/)

2. Para notificações: [Como integrar o Reach (Notificações) em seu aplicativo da Windows Store](../mobile-engagement-windows-store-integrate-engagement-reach/)

3. Implementação do plano de marca: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo da Windows Store](../mobile-engagement-windows-store-use-engagement-api/)

## Notas de versão


## 2.0.0 (17/02/2015)

-   Versão Inicial do Mobile Engagement do Azure
-   A configuração appId/sdkKey é substituída por uma configuração de cadeia de conexão.
-   Aprimoramentos de segurança.

Para a versão anterior, consulte as [notas de versão completas](../mobile-engagement-windows-store-release-notes/)

## Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK, consulte os [procedimentos de atualização](../mobile-engagement-windows-store-upgrade-procedure/) completos. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1" e depois o procedimento "de 0.10.1 a 0.11.0".

## De 1.1.1 a 2.0.0

O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure. 

>[Azure.IMPORTANT] O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.1.1 e depois aplicar o procedimento a seguir

### Pacote NuGet

Substitua o Capptain.WindowsPhone.nupkg azuresdk-mobileengagement-windows-VERSION.nupkg na pasta lib do pacote de arquivo.

### Aplicando o Mobile Engagement

O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a esta alteração.

Você precisa desinstalar o pacote nuget do Capptain atual. Considere que todas as alterações na pasta de recursos Capptain serão removidas. Se você quiser manter esses arquivos, então faça uma cópia deles.

Depois disso, instale o novo pacote nuget do Engagement do Microsoft Azure em seu projeto. Você pode encontrá-lo diretamente no [site do nuget]. ou aqui no índice. Essa ação substitui todos os arquivos de recursos usados pelo Engagement e adiciona a nova DLL do Engagement às suas referências do projeto.

Você precisa limpar as referências do projeto, excluindo as referências de Capptain DLL. Se você não fizer isso, a versão do Capptain entrará em conflito e ocorrerão erros.

Se você personalizou os recursos do Capptain, copie o conteúdo de arquivos antigos e cole-os em novos arquivos do Engagement. Observe que os arquivos xaml e cs devem ser atualizados.

Quando essas etapas forem concluídas, você só precisará substituir as referências antigas do Capptain por novas referências do Engagement.

Todos os namespaces Capptain precisam ser atualizados.

Antes da migração:

			using Capptain.Agent;
			using Capptain.Reach;

Após a migração:

			using Microsoft.Azure.Engagement;

Todas as classes Capptain que contêm "Capptain" devem conter "Engagement".

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

Para os arquivos xaml o namespace e atributos Capptain também se alteram.

Antes da migração:

			<capptain:CapptainPage
			...
			xmlns:capptain="using:Capptain.Agent"
			...
			</capptain:CapptainPage>

Após a migração:

			<engagement:EngagementPage
			...
			xmlns:engagement="using:Microsoft.Azure.Engagement"
			...
			</engagement:EngagementPage>

> [AZURE.IMPORTANT] A sobreposição também será alterada. O novo namespace é `Microsoft.Azure.Engagement.Overlay`. Ele deve ser usado nos arquivos xaml e cs. Além disso `CapptainGrid` será nomeado `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são nomeados `engagement_notification_content` e `engagement_announcement_content`.

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

Para outros recursos como as imagens do Capptain e arquivos HTML, observe que eles também foram renomeados para usar "Engagement".

### Declaração do projeto

Em appxmanifest `File Type Associations` foi atualizado em:

> -   capptain\_reach\_content to engagement\_reach\_content
> -   capptain\_log\_file to engagement\_log\_file

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

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### Alteração do nome de itens

Todos os itens nomeados ...capptain... foi nomeado ...engagement... É o mesmo para Capptain e Engagement.

Exemplos de itens do Capptain usados normalmente:

> -   CapptainConfiguration agora denominado EngagementConfiguration
> -   CapptainAgent agora denominado EngagementAgent
> -   CapptainReach agora denominado EngagementReach
> -   CapptainHttpConfig agora denominado EngagementHttpConfig
> -   GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta os métodos de substituição.

[site do NuGet]:http://www.nuget.org/packages/Capptain.WindowsCS

<!--HONumber=47-->
