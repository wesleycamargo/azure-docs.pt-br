<properties 
	pageTitle="Procedimento de atualização do SDK do iOS no Mobile Engagement do Azure" 
	description="Atualizações e procedimentos mais recentes para o SDK do iOS para Mobile Engagement do Azure"
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

#Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Para cada nova versão do SDK, você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

##De 1.16.0 a 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure. Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.16 e depois aplicar o procedimento a seguir

>[Azure.IMPORTANT]O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement

### Agente

O método `registerApp:` foi substituído pelo novo método `init:`. Seu representante de aplicativo deve ser atualizado de acordo e usar a cadeia de conexão:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

O rastreamento de SmartAd foi removido do SDK, você só precisa remover todas as instâncias da classe `AETrackModule`

### Alterações de nome de classe

Como parte da renovação de marca, há alguns nomes de classe/arquivos que precisam ser alterados.

Todas as classes prefixadas com “CP” foram renomeadas com o prefixo “AE”.

Exemplo:

-   `CPModule.h` foi renomeado para `AEModule.h`.

Todas as classes prefixadas com “Capptain” foram renomeadas com o prefixo “Engagement”.

Exemplos:

-   A classe `CapptainAgent` foi renomeada para `EngagementAgent`.
-   A classe `CapptainTableViewController` foi renomeada para `EngagementTableViewController`.
-   A classe `CapptainUtils` foi renomeada para `EngagementUtils`.
-   A classe `CapptainViewController` foi renomeada para `EngagementViewController`.

<!--HONumber=54--> 