<properties 
	pageTitle="Integração do SDK do Engagement do Windows Universal" 
	description="Como integrar o Mobile Engagement do Azure com aplicativos do Windows Universal" 					
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
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

# Integração do SDK do Engagement do Windows Universal

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a maneira mais simples para ativar as funções de monitoramento e de análise do Engagement em seu aplicativo do Windows Universal.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. O relatório de logs necessário para calcular outras estatísticas, como eventos, erros e trabalhos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement no aplicativo Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)) já que essas estatísticas são dependentes do aplicativo.

## Versões com suporte

O SDK do Mobile Engagement dos Aplicativos do Windows Universal só pode ser integrado a aplicativos do Tempo de Execução do Windows e da Plataforma do Windows Universal destinados ao:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8,1
-   Windows 10 (famílias para dispositivos móveis e desktop)

> [AZURE.NOTE]Se o destino for o Windows Phone Silverlight, então confira o [procedimento de integração do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## Instalação do SDK do Mobile Engagement do Universal

### Todas as plataformas

O SDK do Mobile Engagement para o aplicativo do Windows Universal está disponível como um pacote do Nuget chamado *MicrosoftAzure.MobileEngagement*. Você pode instalá-lo do Visual Studio Nuget Package Manager.

### Windows 8.x e Windows Phone 8.1

O NuGet implanta automaticamente os recursos do SDK na pasta `Resources` na raiz de seu projeto de aplicativo.

### Aplicativos da Plataforma do Windows Universal do Windows 10

O NuGet ainda não implantará automaticamente os recursos do SDK em seu aplicativo UWP. Você deve fazê-lo manualmente até que a implantação de recursos seja reintroduzida no NuGet:

1.  Abra o Explorador de Arquivos.
2.  Navegue até o seguinte local (**x.x.x** é a versão do Engagement que você está instalando): *%USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\**x.x.x** \\content\\win81*
3.  Arraste e solte a pasta **Recursos** do Gerenciador de Arquivos para a raiz de seu projeto no Visual Studio.
4.  No Visual Studio, selecione o projeto e ative o ícone **Mostrar Todos os Arquivos** na parte superior do **Gerenciados de Soluções**.
5.  Alguns arquivos não estão incluídos no projeto. Para importá-los de uma só vez, clique com botão direito do mouse na pasta **Recursos**, **Excluir do projeto** e clique novamente com o botão direito do mouse na pasta **Recursos**, **Incluir no projeto** para voltar a incluir toda a pasta. Todos os arquivos da pasta **Recursos** agora estão incluídos em seu projeto.

O pacote extraído do Engagement também pode ser encontrado em *$(Solutiondir)\\Packages* ou como definido no arquivo *NuGet.config*.

## Adicione recursos

O SDK do Engagement precisa de alguns recursos do SDK do Windows para funcionar corretamente.

Abra o seu arquivo `Package.appxmanifest` e certifique-se de que os seguintes recursos estão declarados:

-   `Internet (Client)`

## Inicialize o SDK do Engagement

### Configuração do Engagement

A configuração do Engagement está centralizada no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de conexão do aplicativo entre as marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### Inicialização do Engagement

Quando você cria um novo projeto, um arquivo `App.xaml.cs` é gerado. Essa classe herda de `Application` e contém vários métodos importantes. Também poderá ser usado para inicializar o SDK do Engagement.

Modifique o `App.xaml.cs`:

-   Adicione às suas instruções `using`:

		using Microsoft.Azure.Engagement;

-   Defina um método para compartilhar a inicialização de Engagement uma vez para todas as chamadas:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
		
		  // or
		
		  EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Chame `InitEngagement` no método `OnLaunched`:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
          InitEngagement(e);
		}

-   Quando o aplicativo é iniciado usando um esquema personalizado, outro aplicativo ou a linha de comando, então o método `OnActivated` é chamado. Você também precisa iniciar o SDK do Engagement quando seu aplicativo é ativado. Para fazer isso, substitua o método `OnActivated`:

		protected override void OnActivated(IActivatedEventArgs args)
		{
          InitEngagement(args);
		}

> [AZURE.IMPORTANT]Nós não recomendamos adicionar a inicialização do Engagement em outro lugar do seu aplicativo.

## Relatórios básicos

### Método recomendado: sobrecarregar suas classes `Page`

Para ativar o relatório de todos os logs exigidos pelo Engagement para computar os usuários, sessões, atividades, falhas e das estatísticas técnicas, você pode simplesmente fazer todas as suas subclasses `Page` herdarem das classes `EngagementPage`.

Este é um exemplo de como fazer isso em uma página do seu aplicativo. Você pode fazer a mesma coisa em todas as páginas do seu aplicativo.

#### Arquivo de código-fonte C#

Modifique o arquivo `.xaml.cs` de paginação:

-   Adicione às suas instruções `using`:

		using Microsoft.Azure.Engagement;

-   Substituir `Page` por `EngagementPage`:

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

> [AZURE.IMPORTANT]Se sua página substitui o método `OnNavigatedTo`, certifique-se de chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada (a `EngagementPage` chama `StartActivity` dentro de seu método `OnNavigatedTo`).

#### Arquivo XAML

Modifique o arquivo `.xaml` de paginação:

-   Adicione às suas declarações de namespaces:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Substituir `Page` por `engagement:EngagementPage`:

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

Esses métodos são chamados de dentro do método `OnNavigatedTo` da página.

### Método alternativo: chame `StartActivity()` manualmente

Se você não pode ou não quer sobrecarregar as classes `Page`, em vez disso, você pode iniciar suas atividades chamando os métodos `EngagementAgent` diretamente.

É recomendável chamar `StartActivity` dentro de seu método `OnNavigatedTo` da sua Página.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]Certifique-se de encerrar a sua sessão corretamente.
> 
> O SDK do Windows Universal chama automaticamente o método `EndActivity` quando o aplicativo é fechado. Desse modo, é **ALTAMENTE** recomendável chamar o método `StartActivity` sempre na atividade de alteração de usuário e **NUNCA** chamar o método `EndActivity`, esse método envia ao servidor do Engagement esse usuário atual que deixa o aplicativo e isso afeta todos os logs de aplicativo.

## Relatórios avançados

Opcionalmente, convém relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, use os outros métodos encontrados na classe `EngagementAgent`. A API do Engagement permite usar todos os recursos avançados do Engagement.

Para obter mais informações, consulte [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Universal](../mobile-engagement-windows-store-use-engagement-api/).

##Configuração avançada

### Desabilitar o relatório de falhas automático

Você pode desabilitar a recurso relatório de falhas automático do Engagement. Então, quando uma exceção não tratada ocorrer, o Engagement não fará nada.

> [AZURE.WARNING]Se você planeja desabilitar esse recurso, lembre-se de que, quando uma falha não tratada ocorrer em seu aplicativo, o Engagement não enviará a falha **E** não fechará a sessão e os trabalhos.

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

### Modo intermitente

Por padrão, o serviço Engagement reporta logs em tempo real. Se seu aplicativo relata logs com muita frequência, é melhor armazenar em buffer os logs e relatá-los todos de uma vez regularmente (isso é chamado de "modo intermitente").

Para fazer isso, chame o método:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você deseja reativar o registro em log em tempo real, basta chamar o método sem nenhum parâmetro, ou com o valor 0.

O modo de intermitência aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). É recomendável usar um limite de intermitência não maior que 30.000 (30s). Você deve estar ciente de que os logs salvos são limitados a 300 itens. Se o envio for muito longo, você poderá perder alguns logs.

> [AZURE.WARNING]O limite de intermitência não pode ser configurado para um período menor que 1s. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será redefinido automaticamente como o valor padrão, ou seja, 0s. Isso irá disparar o SDK para relatar os logs em tempo real.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=AcomDC_1125_2015-->