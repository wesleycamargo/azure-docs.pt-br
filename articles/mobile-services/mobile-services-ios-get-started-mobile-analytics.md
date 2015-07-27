<properties 
	pageTitle="Introdução à Análise Móvel | Mobile Dev Center" 
	description="Introdução à Análise Móvel." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# Introdução à Análise Móvel (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

Neste tutorial, você adiciona recursos de análise móvel ao seu aplicativo usando o [Capptain]. A Análise Móvel permite determinar como os usuários estão interagindo com seu aplicativo e quais seções estão conduzido a maior parte da atividade. Para começar a obter esses dados, você instrumentará o aplicativo com o SDK Capptain.


>[AZURE.NOTE]O Capptain.com de propriedade da Microsoft oferece analítica para aplicativos móveis gratuitamente até 100.000 usuários ativos mensais para clientes dos Serviços Móveis do Azure Camada Padrão. Para aproveitar essa oferta, entre em contato conosco em mobileservices@microsoft.com e obtenha mais instruções. O tutorial a seguir resume os recursos e capacidades do Capptain.com e fornece instruções sobre como usá-los.


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Inicializar o SDK do Capptain]
2. [Sobrecarregar o UIViewController]

Este tutorial exige o seguinte:

* Uma conta [Capptain]
* Um aplicativo [Serviços Móveis camada Padrão]

## <a name="initialize"></a>Inicialize o SDK do Capptain

1. Navegue para a página **Detalhes do aplicativo** do seu aplicativo registrado no Capptain. Selecione a guia SDK e baixe o pacote.

2. No XCode, adicione o SDK Capptain ao seu projeto clicando com o botão direito no projeto e selecionando “Adicionar arquivos a..." Escolha a pasta CapptainSDK.

3. Selecione seu projeto. Na guia **Fases de construção**, selecione **Vincular binário com bibliotecas** e adicione as seguintes estruturas:
    * AdSupport.framework - defina o link como Opcional
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]A estrutura AdSupport pode ser removida. O Capptain precisa dessa estrutura para coletar o IDFA. Porém, a coleta de IDFA pode ser desativada para cumprir a política da Apple sobre essa ID.

4. No arquivo de implantação Representante do Aplicativo, importe o Agente Capptain.


        #import "CapptainAgent.h"


5. Dentro de `applicationDidFinishLaunching:` ou de `application:didFinishLaunchingWithOptions:`, inicialize o Agente Capptain passando `registerapp:identifiedby:` com sua ID do aplicativo e chave de SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Sobrecarregar o UIViewController

1. Encontre cada filho de `UIViewController` no seu projeto e faça com que cada um herde de `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Encontre cada filho de `UITableViewController` no seu projeto e faça com que cada um herde de `CapptainTableViewController`.

    Seu aplicativo agora está configurado para enviar dados de análise para o Capptain.

## Próximas etapas
Saiba mais sobre o que o Capptain pode fazer em seu aplicativo em [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Inicializar o SDK do Capptain]: #initialize
[Sobrecarregar o UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Serviços Móveis camada Padrão]: /pricing/details/mobile-services/
 

<!---HONumber=July15_HO3-->