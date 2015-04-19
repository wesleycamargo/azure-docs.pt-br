<properties 
	pageTitle="Integração do SDK do Windows Phone para o Mobile Engagement do Azure" 
	description="Como integrar o Engagement no Windows Phone" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

# Como integrar o Engagement no Windows Phone

Este procedimento descreve a maneira mais simples para ativar as funções de monitoramento e de análise do Engagement em seu aplicativo do Windows Phone.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. O relatório de logs necessário para calcular outras estatísticas, como eventos, erros e trabalhos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement no aplicativo Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md) abaixo) já que essas estatísticas são dependentes do aplicativo.

## Versões com suporte

O Engagement do Windows Phone SDK só pode ser integrado a aplicativos direcionados ao:

-   Windows Phone 8.0 do SO
-   Windows Phone 8.1 SO com o Silverlight

O Engament do Windows Phone SDK é compatível com o Windows Phone 8.0 e os dispositivos 8.1.

## Incorpore o SDK do Engagement em seu projeto do Windows Phone

O pacote do NuGet do Mobile Engagement ainda não está disponível online, então, é preciso baixar o nosso arquivo SDK, descompactá-lo, e em seguida, no Visual Studio, clique com o botão direito no projeto, vá para "Gerenciar pacotes NuGet", configurações e adicionar a pasta lib como uma nova origem de pacote.


> [AZURE.IMPORTANT] O NuGet é o gerenciador de pacote principal dos projetos do .net. Ele irá baixar, adicionar e copiar tudo o que você precisa usar o SDK do Engagement. No 8 WP o Engagement usa bibliotecas terceirizadas (Microsoft.Bcl.Build v1.0.10 e o pacote de portabilidade BCL v1.1.3 da Microsoft) para fins de portabilidade entre plataformas, o NuGet automaticamente solicitará que você também os instale. Você pode obter mais informações no [site do NuGet](http://docs.nuget.org/docs/start-here/overview).

## Adicione recursos

O SDK do Engagement precisa de alguns recursos do Windows Phone SDK para funcionar corretamente.

Abra o seu `WMAppManifest.xml` arquivo e certifique-se de que os seguintes recursos estão declarados no `Capabilities` painel:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

## Inicialize o SDK do Engagement

### Configuração do Engagement

A configuração do Engagement está centralizada no  `Resources\EngagementConfiguration.xml`o arquivo do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de conexão do aplicativo entre as marcações `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### Inicialização do Engagement

Quando você cria um novo projeto, uma `App.xaml.cs` arquivo é gerado. Essa classe herda de `Application`e contém vários métodos importantes. Também poderá ser usado para inicializar o SDK do Engagement.

Modifique o `App.xaml.cs`:

-   Adicione às suas`using` instruções:

			using Microsoft.Azure.Engagement;

-   Insira `EngagementAgent.Instance.Init` no `Application_Launching` método:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   Insira `EngagementAgent.Instance.OnActivated` no `Application_Activated`método :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Nós não recomendamos adicionar a inicialização do Engagement em outro lugar do seu aplicativo. No entanto, lembre-se que o `EngagementAgent.Instance.Init`método é executado em um thread dedicado e não no thread da interface do usuário.

## Relatórios básicos

### Método recomendado: sobrecarregue sua `PhoneApplicationPage`classes

Para ativar o relatório de todos os logs exigidos pelo Engagement para os usuários, sessões, atividades, falhas e das estatísticas técnicas de computação, você pode simplesmente fazer todas as suas `PhoneApplicationPage`subclasses herdarem as classes de `EngagementPage`.

Este é um exemplo de como fazer isso em uma página do seu aplicativo. Você pode fazer a mesma coisa em todas as páginas do seu aplicativo.

#### Arquivo de código-fonte c#

Modifique a página `.xaml.cs`arquivo:

-   Adicione às suas`using`instruções:

			using Microsoft.Azure.Engagement;

-   Substitua `PhoneApplicationPage`por `EngagementPage` :

**Sem o Engagement:**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**Com o Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] Se sua página herdar o `OnNavigatedTo`método, lembre-se de permitir a `base.OnNavigatedTo(e)`chamada. Caso contrário, a atividade não será registrada. Na verdade, o `EngagementPage`está chamando `StartActivity`dentro do `OnNavigatedTo`método.

#### Arquivo XAML

Modifique a página `.xaml`arquivo:

-   Adicione às suas declarações de namespaces:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Substitua `phone:PhoneApplicationPage`por `engagement:EngagementPage` :

**Sem o Engagement:**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**Com o Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### Substitua o comportamento padrão

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

Mais detalhes sobre as informações adicionais podem ser encontradas [aqui](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Esses métodos são chamados de dentro do método `OnNavigatedTo` da página.

### Método alternativo: chame `StartActivity()` manualmente

Se você não pode ou não quer sobrecarregar as classes `PhoneApplicationPage`, em vez disso, você pode iniciar suas atividades chamando os métodos `EngagementAgent` diretamente.

É recomendável chamar `StartActivity` dentro do método `OnNavigatedTo` da sua PhoneApplicationPage.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Certifique-se de encerrar a sua sessão corretamente.
>
> O Windows Phone SDK chama automaticamente o `EndActivity` método quando o aplicativo é fechado. Portanto, é**ALTAMENTE** recomendado chamar o `StartActivity` método sempre que a atividade do usuário for alterada e**NUNCA** chamar o  `EndActivity`método, visto que chamar esse método força o encerramento da sessão atual.

## Relatórios avançados

Opcionalmente, convém relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, use os outros métodos encontrados na classe `EngagementAgent`. A API do Engagement permite usar todos os recursos avançados do Engagement.

Para obter mais informações, consulte [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo da Windows Store](mobile-engagement-windows-phone-use-the-engagement-api.md).

## Configuração avançada

### Desabilitar o relatório de falhas automático

Você pode desabilitar a recurso relatório de falhas automático do Engagement. Então, quando uma exceção não tratada ocorrer, o Engagement não fará nada.

> [AZURE.WARNING] Se você planeja desabilitar esse recurso, lembre-se de que, quando uma falha não tratada ocorrer em seu aplicativo, o Engagement não enviará a falha **E** ele não fechará a sessão e trabalhos.

Para desativar o relatório de falhas automático, apenas personalize a configuração dependendo de como você o declarou :

#### No arquivo `EngagementConfiguration.xml`

Defina a falha do relatório para `false` entre as marcas `<reportCrash>`e `</reportCrash>`.

#### No objeto `EngagementConfiguration` em tempo de execução

Defina a falha do relatório como false usando o objeto EngagementConfiguration.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### Modo de intermitência [beta]

Por padrão, os relatórios de serviço do Engagement registram em log em tempo real. Se seu aplicativo relata logs com muita frequência, é melhor armazenar em buffer os logs e relatá-los todos de uma vez regularmente (isso é chamado de "modo de intermitência").

Para fazer isso, chame o método:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você deseja reativar o registro em log em tempo real, basta chamar o método sem nenhum parâmetro, ou com o valor 0.

O modo de intermitência aumenta um pouco a vida útil da bateria, mas tem um impacto sobre o Monitor do Engagement: toda a duração de sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos menores do que o limite de intermitência pode não estar visível). É recomendável usar um limite de intermitência por não mais que 30000 (30s).

> [AZURE.WARNING] O limite de intermitência não pode ser configurado para um período menor que um segundo. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será redefinido automaticamente como o valor padrão, que é de, zero segundo. Isso irá disparar o SDK para relatar os logs em tempo real.

<!--HONumber=47-->
