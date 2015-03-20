<properties 
	pageTitle="Integração do SDK do iOS do Azure Mobile Engagement" 
	description="Atualizações mais recentes e procedimentos para o SDK do iOS do Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


#Como integrar o Engagement no iOS

> [AZURE.IMPORTANT] O SDK do Engagement exige iOS4+: o destino da implantação do seu aplicativo deve ter pelo menos o iOS 4.

Este procedimento descreve a maneira mais simples de ativar as funções de Analítica e Monitoramento do Engagement em seu aplicativo iOS.

As etapas a seguir são suficientes para ativar o relatório de logs necessário para calcular todas as estatísticas sobre Usuários, Sessões, Atividades, Falhas e Suporte Técnico. O relatório de logs necessários para calcular outras estatísticas, como Trabalhos, Eventos e Erros deve ser feito manualmente usando a API do Engagement (consulte ios-sdk-engagement-advanced) como essas estatísticas são dependentes do aplicativo.

##Incorporar o SDK do Engagement em seu projeto do iOS

Adicione o SDK do Engagement ao seu projeto do iOS: no Xcode 4, clique com o botão direito do mouse no seu projeto e selecione **"Adicionar arquivos a..."** e escolha a pasta  `EngagementSDK`.

O Engagement exige estruturas adicionais para funcionar: no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Compilar fases"** e no menu **"Link binário com bibliotecas"** e adicione essas estruturas:

> -   `AdSupport.framework` -Defina o link como  `opcional`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] A estrutura AdSupport pode ser removida. O Engagement precisa dessa estrutura para coletar o IDFA. Porém, a coleta de IDFA pode ser desabilitada \<ios-sdk-engagement-idfa\> para cumprir com a nova política da Apple sobre essa ID.

##Inicializar o SDK do Engagement

Você precisa modificar seu representante de Aplicativo:

-   Na parte superior do seu arquivo de implementação, importe o agente do Engagement:

			[...]
			#import "EngagementAgent.h"

-   Inicialize o Engagement dentro do método '**applicationDidFinishLaunching:**'ou'**application: didFinishLaunchingWithOptions:**':

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

##Relatórios básicos

### Método recomendado: sobrecarregar suas classes `UIViewController` 

Para ativar o relatório de todos os logs exigidos pelo Engagement para calcular os Usuários, Sessões, Atividades, Falhas e Estatísticas Técnicas, você pode simplesmente fazer todas as suas subclasses  `UIViewController` herdarem as classes  `EngagementViewController` (mesma regra para  `UITableViewController` -\ > `EngagementTableViewController`).

**Sem o Engagement:**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Com o Engagement:**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Método alternativo: chamar o  `startActivity()` manualmente

Se você não pode ou não quer sobrecarregar as suas classes  `UIViewController`, em vez disso, é possível iniciar suas atividades chamando  `EngagementAgent` de métodos diretamente.

> [AZURE.IMPORTANT] O SDK do iOS chama automaticamente o método  `endActivity()` quando o aplicativo é fechado. Portanto, é  *ALTAMENTE* recomendado chamar o método  `startActivity` sempre que alterar a atividade do usuário e  *NUNCA* chamar o método  `endActivity`, pois chamar esse método força a sessão atual a ser encerrada.

##Relatórios de local

Os termos de serviço da Apple não permitem que os aplicativos usem o local apenas para fins de estatísticas de acompanhamento. Assim, é recomendável habilitar relatórios locais somente se o seu aplicativo também usar o acompanhamento de local por outro motivo.

A partir do iOS 8, você deve fornecer uma descrição de como o seu aplicativo usa os serviços de localização, definindo uma cadeia de caracteres para a chave [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] no arquivo Info.plist do seu aplicativo. Se você quiser o local do relatório em segundo plano com o Engagement, adicione a chave NSLocationAlwaysUsageDescription. Em outros casos, adicione a chave NSLocationWhenInUseUsageDescription.

### Relatórios de local de área lenta

O relatório de local de área lenta permite relatar o país, a região e a localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem pouco impacto (ou quase nenhum) sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach. A última área conhecida relatada para um dispositivo pode ser recuperada graças à [API do dispositivo].

Para habilitar o relatório de local de área lenta, adicione a seguinte linha depois de inicializar o agente de Engagement:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### Relatórios de local em tempo real

Os relatórios de local em tempo real permitem relatar a latitude e a longitude associados aos dispositivos. Por padrão, esse tipo de relatório local usa apenas os locais de rede (com base na ID da célula ou WIFI) e o relatório estará disponível apenas quando o aplicativo for executado em primeiro plano (ou seja, durante uma sessão).

Os locais em tempo real  *NÃO* são usados para calcular estatísticas. Sua única finalidade é permitir o uso em tempo real
Critério geo-fencing \<Reach-Audience-geofencing\> nas campanhas do Reach.

Para habilitar o relatório de local em tempo real, adicione a seguinte linha depois de inicializar o agente do Engagement:

			[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### Relatórios com base em GPS

Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), adicione:

			[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Relatório de segundo plano

Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (ou seja, durante uma sessão). Para habilitar o relatório também em segundo plano, adicione:

			[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.

A implementação dessa função chamará [startMonitoringSignificantLocationChanges] quando o aplicativo entra em segundo plano. Lembre-se de que ele reinicia automaticamente o seu aplicativo em segundo plano caso chegue um novo evento local.

##Relatórios avançados

Opcionalmente, se você deseja relatar trabalhos, erros e eventos específicos do aplicativo, você precisa usar a API do Engagement por meio dos métodos da classe `EngagementAgent`. Um objeto dessa classe pode ser recuperado chamando o método estático `[EngagementAgent shared]`.

A API do Engagement permite usar todos os recursos avançados do Engagement e detalhada em Como usar a
API do Engagement no iOS (além da documentação técnica da classe  `EngagementAgent`).

##Desabilitar a coleta de IDFA

Por padrão, o Engagement usará o [IDFA] para identificar exclusivamente um usuário. Mas se não estiver usando anúncios em outro lugar no aplicativo, você pode ser rejeitado pelo processo de revisão do App Store. A coleção IDFA pode ser desabilitada, adicionando a macro do pré-processador `ENGAGEMENT_DISABLE_IDFA` em seu arquivo pch (ou em  `Compilar configurações` de seu aplicativo). Isso garantirá que não há nenhuma referência a  `ASIdentifierManager`, a  `advertisingIdentifier` ou a  `isAdvertisingTrackingEnabled` na compilação do aplicativo.

Integração no arquivo **prefix.pch**:

			#define ENGAGEMENT_DISABLE_IDFA
			...

Você pode verificar se a coleção IDFA está corretamente desabilitada em seu aplicativo, verificando os logs de teste do Engagement. Consulte a documentação de integração do Test\<ios-sdk-engagement-test-idfa\> para obter mais informações.

##Desabilitar o relatório de log

### Usando uma chamada de método

Se desejar que o Engagement pare de enviar logs, você pode chamar:

			[[EngagementAgent shared] setEnabled:NO];

Essa chamada é persistente: usa  `NSUserDefaults` para armazenar as informações.

Você pode habilitar o log de relatórios novamente chamando a mesma função com  `SIM`.

### Integração no pacote de configurações

Em vez de chamar essa função, você também pode integrar essa configuração diretamente no seu arquivo  `Settings.bundle`. A cadeia de caracteres  `engagement_agent_enabled` deve ser usada como um identificador de preferência e ele deve estar associado a um switch de alternância (`PSToggleSwitchSpecifier`).

O exemplo a seguir de  `Settings.bundle` mostra como implementá-lo:

			<dict>
			    <key>PreferenceSpecifiers</key>
			    <array>
			        <dict>
			            <key>DefaultValue</key>
			            <true/>
			            <key>Key</key>
			            <string>engagement_agent_enabled</string>
			            <key>Title</key>
			            <string>Log reporting enabled</string>
			            <key>Type</key>
			            <string>PSToggleSwitchSpecifier</string>
			        </dict>
			    </array>
			    <key>StringsTable</key>
			    <string>Root</string>
			</dict>

<!-- URLs. -->
[API do dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
