<properties 
	pageTitle="Integração do SDK do Windows Phone Silverlight para o Engagement" 
	description="Como integrar o Mobile Engagement do Azure com aplicativos do Windows Phone Silverlight" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Integração do SDK do Windows Phone Silverlight para o Engagement

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a maneira mais simples para ativar as funções de monitoramento e de análise do Mobile Engagement do Azure em seu aplicativo do Windows Phone Silverlight.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. O relatório de logs necessário para calcular outras estatísticas, como eventos, erros e trabalhos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement no aplicativo Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md) abaixo) já que essas estatísticas são dependentes do aplicativo.

##Versões com suporte

O SDK do Mobile Engagement do Windows Silverlight só pode ser integrado a aplicativos destinados ao:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE]Se o destino for o Windows Phone 8.1 (sem Silverlight), então consulte o [procedimento de integração do Windows Universal](mobile-engagement-windows-store-integrate-engagement.md).

##Instalação do SDK do Silverlight do Mobile Engagement

O SDK do Mobile Engagement Windows Silverlight está disponível como um pacote do Nuget chamado *MicrosoftAzure.MobileEngagement*. Você pode instalá-lo do Visual Studio Nuget Package Manager.

##Adicione recursos

O SDK do Engagement precisa de alguns recursos do SDK do Windows Phone Silverlight para funcionar corretamente.

Abra o seu arquivo `WMAppManifest.xml` e certifique-se de que os seguintes recursos estão declarados no painel `Capabilities`:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Inicialize o SDK do Engagement

### Configuração do Engagement

A configuração do Engagement está centralizada no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar :

-   A cadeia de conexão do aplicativo entre as marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão do seu aplicativo é exibida no Portal Clássico do Azure.

### Inicialização do Engagement

Quando você cria um novo projeto, um arquivo `App.xaml.cs` é gerado. Essa classe herda de `Application` e contém vários métodos importantes. Também poderá ser usado para inicializar o SDK do Engagement.

Modifique o `App.xaml.cs`:

-   Adicione às suas instruções `using`:

		using Microsoft.Azure.Engagement;

-   Insira `EngagementAgent.Instance.Init` no método `Application_Launching` :

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		  EngagementAgent.Instance.Init();
		}

-   Insira `EngagementAgent.Instance.OnActivated` no método `Application_Activated` :

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		}

> [AZURE.WARNING]Nós não recomendamos adicionar a inicialização do Engagement em outro lugar do seu aplicativo. No entanto, lembre-se que o método `EngagementAgent.Instance.Init` é executado em um thread dedicado e não no thread da interface do usuário.

##Relatórios básicos

### Método recomendado: sobrecarregar suas classes `PhoneApplicationPage`

Para ativar o relatório de todos os logs exigidos pelo Engagement para os usuários, sessões, atividades, falhas e das estatísticas técnicas de computação, você pode simplesmente fazer todas as suas subclasses `PhoneApplicationPage` herdarem das classes `EngagementPage`.

Este é um exemplo de como fazer isso em uma página do seu aplicativo. Você pode fazer a mesma coisa em todas as páginas do seu aplicativo.

#### Arquivo de código-fonte C#

Modifique o arquivo `.xaml.cs` de paginação :

-   Adicione às suas instruções `using`:

		using Microsoft.Azure.Engagement;

-   Substituir `PhoneApplicationPage` por `EngagementPage` :

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

> [AZURE.WARNING]Se sua página herdar o método `OnNavigatedTo`, lembre-se de permitir a chamada `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada. Na verdade, o `EngagementPage` está chamando `StartActivity` dentro do método `OnNavigatedTo`.

#### Arquivo XAML

Modifique o arquivo `.xaml` de paginação :

-   Adicione às suas declarações de namespaces :

		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Substituir `phone:PhoneApplicationPage` por `engagement:EngagementPage` :

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

Esses métodos são chamados de dentro do método `OnNavigatedTo` da página.

### Método alternativo: chame `StartActivity()` manualmente

Se você não pode ou não quer sobrecarregar as classes `PhoneApplicationPage`, em vez disso, você pode iniciar suas atividades chamando os métodos `EngagementAgent` diretamente.

É recomendável chamar `StartActivity` dentro do método `OnNavigatedTo` da sua PhoneApplicationPage.

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		   base.OnNavigatedTo(e);
		   EngagementAgent.Instance.StartActivity("MyPage");
		}

> [AZURE.IMPORTANT]Certifique-se de encerrar a sua sessão corretamente.
>
> O SDK do iOS chama automaticamente o método `EndActivity` quando o aplicativo é fechado. Portanto, é **ALTAMENTE** recomendado chamar o método `StartActivity` sempre que a atividade do usuário for alterada e **NUNCA** chamar o método `EndActivity`. Esse método envia uma mensagem para o servidor Engagement que o usuário atual tenha deixado o aplicativo e isso afeta todos os logs de aplicativo.

##Relatórios avançados

Opcionalmente, convém relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, use os outros métodos encontrados na classe `EngagementAgent`. A API do Engagement permite usar todos os recursos avançados do Engagement.

Para obter mais informações, consulte [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo da Windows Phone Silverlight](../mobile-engagement-windows-phone-use-engagement-api/).

##Configuração avançada

### Desabilitar o relatório de falhas automático

Você pode desabilitar a recurso relatório de falhas automático do Engagement. Então, quando uma exceção não tratada ocorrer, o Engagement não fará nada.

> [AZURE.WARNING]Se você planeja desabilitar esse recurso, lembre-se de que, quando uma falha não tratada ocorrer em seu aplicativo, o Engagement não enviará a falha **E** ele não fechará a sessão e trabalhos.

Para desativar o relatório de falhas automático, apenas personalize a configuração dependendo de como você o declarou :

#### No arquivo `EngagementConfiguration.xml`

Defina a falha do relatório para `false` entre as marcas `<reportCrash>` e `</reportCrash>`.

#### No objeto `EngagementConfiguration` em tempo de execução

Defina a falha do relatório como false usando o objeto EngagementConfiguration.

		/* Engagement configuration. */

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### Modo intermitente

Por padrão, o serviço Engagement reporta logs em tempo real. Se seu aplicativo relata logs com muita frequência, é melhor armazenar em buffer os logs e relatá-los todos de uma vez regularmente (isso é chamado de "modo intermitente").

Para fazer isso, chame o método:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você deseja reativar o registro em log em tempo real, basta chamar o método sem nenhum parâmetro, ou com o valor 0.

O modo de intermitência aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). É recomendável usar um limite de intermitência não maior que 30.000 (30s). Você deve estar ciente de que os logs salvos são limitados a 300 itens. Se o envio for muito longo, você poderá perder alguns logs.

> [AZURE.WARNING]O limite de intermitência não pode ser configurado para um período menor que um segundo. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será redefinido automaticamente como o valor padrão, que é de, zero segundo. Isso irá disparar o SDK para relatar os logs em tempo real.
 

<!---HONumber=AcomDC_1203_2015-->