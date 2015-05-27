<properties 
	pageTitle="Integração do SDK do Android do Azure Mobile Engagement" 
	description="Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
	services="mobile-engagement" 
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
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Como integrar o Engagement ao Android

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a maneira mais simples de ativar as funções de Análise e Monitoramento do Engagement em seu aplicativo Android.

> [AZURE.IMPORTANT]O nível mínimo de API do Android SDK deve ser 10 ou superior (Android 2.3.3 ou superior).
 
As etapas a seguir são suficientes para ativar o relatório de logs necessário para calcular todas as estatísticas referentes a Usuários, Sessões, Atividades, Falhas e Suporte Técnico. O relatório de logs necessários para calcular outras estatísticas, como Trabalhos, Erros e Eventos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement em seu Android](mobile-engagement-android-use-engagement-api.md) já que essas estatísticas são dependentes do aplicativo.

##Incorpore o SDK e o serviço do Engagement em seu projeto Android

Obtenha `mobile-engagement-VERSION.jar` e coloque-os na pasta `libs` do seu projeto Android (crie a pasta das bibliotecas se ela ainda não existir).

> [AZURE.IMPORTANT]Se você compilar seu pacote de aplicativo com o ProGuard, você precisa manter algumas classes. Você pode usar o seguinte trecho de código de configuração:
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Especifique a cadeia de conexão do Engagement chamando o método a seguir na atividade de inicializador:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

-   Se estiver faltando, adicione as seguintes permissões do Android (antes da marca `<application>`):

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Em alguns modelos de dispositivo, não é possível gerar o identificador de dispositivo do Engagement por meio do ANDROID_ID (pode conter bugs ou estar indisponível). Nesse caso, o SDK gera um identificador aleatório de dispositivo e tenta salvá-lo no armazenamento externo desse dispositivo, para que outros aplicativos do Engagement possam compartilhar o mesmo identificador de dispositivo (ele também é salvo como uma preferência compartilhada, para garantir que o próprio aplicativo use sempre o mesmo identificador de dispositivo, independentemente do que acontecer com o armazenamento externo). Para que esse mecanismo funcione corretamente, você precisa adicionar a permissão a seguir se ele estiver ausente (antes da marca `<application>`):

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

-   Adicione a seção a seguir (entre as marcas `<application>` e `</application>`):

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Substitua `<Your application name>` pelo nome do seu aplicativo.

> [AZURE.TIP]O atributo `android:label` permite que você escolha o nome do serviço do Engagement como ele aparecerá para os usuários finais na tela "Serviços em execução" dos seus respectivos telefones. É recomendável definir esse atributo para `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o atributo `android:process` garante que o serviço do Engagement será executado em seu próprio processo (executando o Engagement no mesmo processo que seu aplicativo fará o thread/interface de usuário principal potencialmente menos responsivos).

> [AZURE.NOTE]Qualquer código que você colocar em `Application.onCreate()` e outros retornos de chamada do aplicativo serão executados para todos os processos de seu aplicativo, incluindo o serviço Engagement. Ele pode ter efeitos colaterais indesejados (como alocações desnecessárias e threads no processo do Engagement, serviços ou receptores de difusão duplicados).

Se você substituir `Application.onCreate()`, recomenda-se adicionar o seguinte trecho de código no início da sua função `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de ampliar `Application`: o retorno de chamada `Application.onCreate()` faz a verificação do processo e chama `Application.onApplicationProcessCreate()` somente se o processo atual não for aquele que hospeda o serviço do Engagement. As mesmas regras se aplicam para os outros retornos de chamada.

##Relatórios básicos

### Método recomendado: sobrecarregar suas classes `Activity`

Para ativar o relatório de todos os logs exigidos pelo Engagement para calcular os Usuários, Sessões, Atividades, Falhas e Estatísticas técnicas, você só precisa fazer com que todas as suas subclasses `*Activity` herdem classes `Engagement*Activity` correspondentes (por exemplo, se sua atividade herdada estende `ListActivity`, faça com que ela estenda `EngagementListActivity`).

**Sem o Engagement :**

			package com.company.myapp;
			
			import android.app.Activity;
			import android.os.Bundle;
			
			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**Com o Engagement :**

			package com.company.myapp;
			
			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;
			
			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT]Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se de que qualquer chamada para `requestWindowFeature(...);` seja feita antes da chamada para `super.onCreate(...);`, caso contrário, ocorrerá uma falha.

Fornecemos subclasses de `FragmentActivity` e `MapActivity`, mas para evitar problemas com aplicativos que usam **ProGuard**, não as incluímos em `engagement.jar`.

Você pode encontrar essas classes na pasta `src` e copiá-las para seu projeto. As classes também estão no **JavaDoc**.

### Método alternativo: chame `startActivity()` e `endActivity()` manualmente

Se você não pode ou não quer sobrecarregar as suas classes `Activity`, é possível iniciar suas atividades chamando diretamente os métodos de `EngagementAgent`.

> [AZURE.IMPORTANT]O SDK do Android nunca chama o método `endActivity()`, mesmo quando o aplicativo é fechado (no Android, aplicativos nunca são, na verdade, fechados). Assim, é *ALTAMENTE* recomendado chamar o método `startActivity()` no retorno de chamada `onResume` de *TODAS* as suas atividades, e o método `endActivity()` no retorno de chamada `onPause()` de *TODAS* as suas atividades. Essa é a única maneira de certificar-se de que as sessões não serão perdidas. Se ocorrer perda de uma sessão, o serviço Engagement nunca se desconectará do back-end do Engagement (desde que o serviço permaneça conectado enquanto houver uma sessão pendente).

Aqui está um exemplo:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }
			
			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Este exemplo é muito semelhante à classe `EngagementActivity` e suas variantes, cujo código-fonte é fornecido na pasta `src`.

##Teste

Agora, verifique sua integração lendo Como Testar a Integração do Engagement em Android.

As seções a seguir são opcionais.

##Relatórios de local

Se quiser que locais sejam informados, você precisa adicionar algumas linhas de configuração (entre as marcas `<application>` e `</application>`).

### Relatórios de local de área lenta

O relatório de local de área lenta permite relatar o país, a região e a localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem pouco impacto (ou quase nenhum) sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach. A última área conhecida relatada para um dispositivo pode ser recuperada graças à [API do dispositivo].

Para habilitar o relatório de local de área lenta, adicione:

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

Você também precisa adicionar a permissão a seguir, se ausente:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### Relatórios de local em tempo real

Os relatórios de local em tempo real permitem relatar a latitude e a longitude associados aos dispositivos. Por padrão, esse tipo de relatório local usa apenas os locais de rede (com base na ID da célula ou WIFI) e o relatório estará disponível apenas quando o aplicativo for executado em primeiro plano (ou seja, durante uma sessão).

Os locais em tempo real *NÃO* são usados para calcular estatísticas. Sua única finalidade é permitir o uso do critério de isolamento geográfico em tempo real <Reach-Audience-geofencing> em Campanhas de alcance.

Para habilitar o relatório de local em tempo real, adicione:

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

Você também precisa adicionar a permissão a seguir, se estiver ausente:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### Relatórios com base em GPS

Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), adicione:

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

Você também precisa adicionar a permissão a seguir, se estiver ausente:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Relatório de segundo plano

Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (ou seja, durante uma sessão). Para habilitar o relatório também em segundo plano, adicione:

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

> [AZURE.NOTE]Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.

O relatório de local de segundo plano será interrompido se o usuário reiniciar o dispositivo; você pode adicionar isso para fazê-lo reiniciar automaticamente no momento de inicialização:

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

Você também precisa adicionar a permissão a seguir, se estiver ausente:

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

##Relatórios avançados

Opcionalmente, se deseja relatar trabalhos, erros e eventos específicos do aplicativo, você precisa usar a API do Engagement por meio dos métodos da classe `EngagementAgent`. Um objeto dessa classe pode ser recuperado chamando o método estático `EngagementAgent.getInstance()`.

A API do Engagement permite usar todos os recursos avançados do Engagement e é detalhada em Como usar a API do Engagement em Android (além da documentação técnica da classe `EngagementAgent`).

##Configuração avançada (em Androidmanifest.xml)

Se quiser ter certeza de que as estatísticas são enviadas em tempo real ao usar Wifi ou quando a tela estiver desligada, adicione a seguinte permissão opcional:

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

Se você deseja desabilitar relatórios de falha, adicione (entre as marcas `<application>` e `</application>`):

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

Por padrão, o serviço Engagement reporta logs em tempo real. Se seu aplicativo reporta logs com muita frequência, é melhor armazenar os logs em buffer e relatá-los todos de uma vez em uma base de tempo normal (isso é chamado de "modo de intermitência"). Para fazer isso, adicione (entre as marcas `<application>` e `</application>`):

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

O modo de intermitência aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). É recomendável usar um limite de intermitência não maior que 30.000 (30s).

Por padrão, o serviço Engagement estabelece a conexão com nossos servidores assim que a rede está disponível. Se você deseja adiar a conexão, adicione isso (entre as marcas `<application>` e `</application>`):

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

Por padrão, uma sessão é encerrada 10s após o término de sua última atividade (que geralmente ocorre pressionando a tecla Página Inicial ou Voltar, definindo o telefone como ocioso ou indo diretamente para outro aplicativo). Isso é para evitar uma divisão de sessão cada vez que o usuário sair e retornar para o aplicativo rapidamente (o que pode acontecer quando ele pegar uma imagem, verificar uma notificação, etc.). Convém modificar esse parâmetro. Para fazer isso, adicione (entre as marcas `<application>` e `</application>`):

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Desabilitar o relatório de log

### Usando uma chamada de método

Se desejar que o Engagement pare de enviar logs, você pode chamar:

			EngagementAgent.getInstance(context).setEnabled(false);

Essa chamada é persistente: ela utiliza um arquivo de preferências compartilhado.

Se o Engagement está ativo quando você chama essa função, pode levar um minuto para parar o serviço. No entanto, ele nem sequer abrirá o serviço na próxima vez que você iniciar o aplicativo.

Você pode habilitar o log de relatórios novamente chamando a mesma função com `true`.

### Integração em seu próprio `PreferenceActivity`

Em vez de chamar essa função, você também pode integrar esta configuração diretamente em seu arquivo existente `PreferenceActivity`.

Você pode configurar o Engagement para usar o arquivo de preferências (com o modo desejado) no arquivo `AndroidManifest.xml` com `application meta-data`:

-   A chave `engagement:agent:settings:name` é usada para definir o nome do arquivo de preferências compartilhado.
-   A chave `engagement:agent:settings:mode` é usada para definir o modo do arquivo de preferências compartilhado. Você deve usar o mesmo modo que em seu `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

O Engagement sempre usa a chave booliana `engagement:key` dentro do arquivo de preferências para gerenciar esta configuração.

O exemplo a seguir de `AndroidManifest.xml` mostra os valores padrão:

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Em seguida, você pode adicionar um `CheckBoxPreference` em seu layout de preferência como o seguinte:

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[API do dispositivo]: http://go.microsoft.com/?linkid=9876094

<!--HONumber=54-->