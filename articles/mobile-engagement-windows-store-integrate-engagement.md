<properties 
	pageTitle="Visão geral SDK da Windows Store para o Mobile Engagement do Azure" 
	description="Atualizações mais recentes e procedimentos para o SDK da Windows Store para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
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

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>Android

# Como integrar o Engagement no Windows

Este procedimento descreve a maneira mais simples para ativar as funções de monitoramento e de análise do Engagement em seu aplicativo do Windows.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. O relatório de logs necessário para calcular outras estatísticas, como eventos, erros e trabalhos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement no aplicativo Windows Store](../mobile-engagement-windows-store-use-engagement-api/)) já que essas estatísticas são dependentes do aplicativo..

## Versões com suporte

O SDK do Windows só oferece suporte ao:

-   Aplicativo C\# Store do Windows 8
-   Aplicativo C\# Store do Windows 8.1

## Incorporar o SDK do Engagement no seu projeto do Windows

Inserir o SDK do Engagement o usando o Gerenciador de Pacotes NuGet para a plataforma de desenvolvimento da Microsoft.

O pacote do NuGet do Mobile Engagement ainda não está disponível online, então, é preciso baixar o nosso arquivo SDK, descompactá-lo, e em seguida, no Visual Studio, clique com o botão direito no projeto, vá para "Gerenciar pacotes NuGet", configurações e adicionar a pasta lib como uma nova origem de pacote.

> [AZURE.NOTE] O NuGet é o gerenciador de pacote principal dos projetos do .net. Ele irá baixar, adicionar e copiar tudo o que você precisa para usar o SDK do Engagement. O aplicativo C\# Metro do Windows 8 para Engagement usa bibliotecas de terceiros (Pacote de portabilidade v1.1.3 do Microsoft.Bcl.Build v1.0.10 e Microsoft.Bcl) para fins de portabilidade entre plataformas, o NuGet automaticamente solicitará que você instale-os também. Você pode obter mais informações no [site do NuGet]

## Adicione recursos

O SDK do Engagement precisa de alguns recursos do SDK do Windows para funcionar corretamente.

Abra o seu arquivo`Package.appxmanifest` e certifique-se de que os seguintes recursos estão declarados:

-   `Internet (Client)`

Vá para o painel `Declarations` do arquivo Package.appxmanifest.

Em `Declarações Disponíveis`selecione `Associações de Tipo de Arquivo` e adicione-o. Na tela da direita, no campo `Nome`, escreva `engagement_log_file` e no campo Tipo de arquivo, escreva `.set`.

Em seguida, em `Declarações Disponíveis` selecione `Atualizador do Arquivo em Cache` e adicione-o.

## Inicialize o SDK do Engagement

### Configuração do Engagement

A configuração do Engagement está centralizada no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de conexão do aplicativo entre as marcas `<connectionString>`e`<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### Inicialização do Engagement

Quando você cria um novo projeto, um arquivo `App.xaml.cs` é gerado. Essa classe herda do `Aplicativo` e contém vários métodos importantes. Também poderá ser usado para inicializar o SDK do Engagement.

Modifique o `App.xaml.cs`::

-   Adicione às suas instruções`using`:

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   Quando o aplicativo é iniciado usando um esquema personalizado, outro aplicativo ou a linha de comando, então o método `OnActivated` é chamado. Você também precisa iniciar o agente do Engagement quando seu aplicativo é ativado. Para fazer isso, substitua o método `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] Nós não recomendamos adicionar a inicialização do Engagement em outro lugar do seu aplicativo.

## Relatórios básicos

### Método recomendado: sobrecarregar suas classes `Page`

Para ativar o relatório de todos os logs exigidos pelo Engagement para os usuários, sessões, atividades, falhas e das estatísticas técnicas de computação, você pode simplesmente fazer todas as suas subclasses `Page` herdarem das classes `EngagementPage` .

Este é um exemplo de como fazer isso em uma página do seu aplicativo. Você pode fazer a mesma coisa em todas as páginas do seu aplicativo.

#### Arquivo de código-fonte c#

Modifique o arquivo de paginação `.xaml.cs`:

-   Adicione às suas instruções`using`:

			using Microsoft.Azure.Engagement;

-   Substitua `Page`por `EngagementPage` :

**Sem o Engagement:**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**Com o Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] Se sua página substitui o método `OnNavigatedTo`, certifique-se de chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada (a `EngagementPage`chama `StartActivity` dentro de seu método `OnNavigatedTo`).

#### Arquivo XAML

Modifique o arquivo de paginação `.xaml`:

-   Adicione às suas declarações de namespaces:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Substitua `Page`por `engagement:EngagementPage` :

**Sem o Engagement:**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**Com o Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
			</engagement:EngagementPage >

#### Substituir o comportamento padrão

Por padrão, o nome da classe da página é relatado como o nome da atividade, com e sem extras. Se a classe usa o sufixo "Página", o Engagement irá removê-lo também.

Se você quiser substituir o comportamento padrão para o nome, basta adicionar isso ao seu código:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			  /* your code */
			  return "new name";
			}

Se você desejar relatar algumas informações adicionais com a sua atividade, você pode adicionar isso ao seu código:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			  /* your code */
			  return extra;
			}

Mais detalhes sobre as informações adicionais podem ser encontradas aqui \<windows-sdk-extras-parameters\>.

Esses métodos são chamados de dentro do método `OnNavigatedTo` da página.

### Método alternativo: chame `StartActivity()` manualmente

Se você não pode ou não quer sobrecarregar as classes `Page`, em vez disso, você pode iniciar suas atividades chamando os métodos `EngagementAgent` diretamente.

É recomendável chamar `StartActivity` dentro do método `OnNavigatedTo` da sua página.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] O SDK do Windows chama automaticamente o método `EndActivity` quando o aplicativo é fechado. Portanto, é *HIGHLY* recomendado chamar o método `StartActivity` sempre que a atividade do usuário é alterada e *NEVER* chamar o `EndActivity` método, visto que chamar esse método força o encerramento da sessão atual.

## Relatórios avançados

Opcionalmente, convém relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, use os outros métodos encontrados na classe `EngagementAgent`. A API do Engagement permite usar todos os recursos avançados do Engagement.

Para obter mais informações, consulte Como usar a API do Engagement no Windows.

## Configuração avançada

### Desabilitar o relatório de falhas automático

Você pode desabilitar a recurso relatório de falhas automático do Engagement. Então, quando uma exceção não tratada ocorrer, o Engagement não fará nada.

> [AZURE.WARNING] Se você planeja desabilitar esse recurso, lembre-se de que, quando uma falha não tratada ocorrer em seu aplicativo, o Engagement não enviará a falha **E** não fechará a sessão e trabalhos.

Para desativar o relatório de falhas automático, apenas personalize a configuração dependendo de como você o declarou:

#### No arquivo `EngagementConfiguration.xml`

Defina a falha do relatório para `false` entre as marcas `<reportCrash>` e `</reportCrash>`.

#### No objeto `EngagementConfiguration` em tempo de execução

Defina a falha do relatório como false usando o objeto EngagementConfiguration.

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### Modo de intermitência [beta]

Por padrão, os relatórios de serviço do Engagement registram em log em tempo real. Se seu aplicativo relata logs com muita frequência, é melhor armazenar em buffer os logs e relatá-los todos de uma vez regularmente (isso é chamado de "modo de intermitência").

Para fazer isso, chame o método:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você deseja reativar o registro em log em tempo real, basta chamar o método sem nenhum parâmetro, ou com o valor 0.

O modo de intermitência aumenta um pouco a vida útil da bateria, mas tem um impacto sobre o Monitor do Engagement: toda a duração de sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos menores do que o limite de intermitência pode não estar visível). É recomendável usar um limite de intermitência por não mais que 30000 (30s).

> [AZURE.WARNING] O limite de intermitência não pode ser configurado para um período menor que 1s. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será redefinido automaticamente como o valor padrão, ou seja, 0s. Isso irá disparar o SDK para relatar os logs em tempo real.

[aqui]:http://www.nuget.org/packages/Capptain.WindowsCS
[Site do NuGet]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
