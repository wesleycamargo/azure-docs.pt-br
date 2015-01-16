<properties urlDisplayName="Using Offline Data" pageTitle="Usar a sincronização de dados Offline nos Serviços Móveis (iOS) | Mobile Dev Center" metaKeywords="" description="Saiba como usar serviços móveis do Azure para cache e sincronização de dados offline no seu aplicativo iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Introdução à sincronização de dados offline nos Serviços Móveis


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Este tutorial abrange o recurso de sincronização offline dos Serviços Móveis em iOS, o qual permite gravar aplicativos utilizáveis quando o usuário final não tem acesso à rede.

A sincronização offline possui diversos usos possíveis:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Torna os aplicativos resilientes em relação à conectividade da rede intermitente
* Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
* Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos

Este tutorial mostrará como atualizar o aplicativo por meio do tutorial [Introdução aos Serviços Móveis], para dar suporte a recursos offline dos Serviços Móveis do Azure. Você adicionará dados em um cenário offline desconectado, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

>[WACOM.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>.

O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Desse modo, este tópico explica muitas das etapas que são concluídas para você no Guia de início rápido dos Serviços Móveis. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].

>[WACOM.NOTE] Você pode ignorar estas seções e ir direto para baixar uma versão do projeto da Introdução, que já tem suporte offline e tudo mais descrito neste tópico.  Para baixar um projeto com suporte offline habilitado, consulte [Introdução à Amostra Offline para o iOS].


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Obter a amostra de aplicativo de início rápido]
2. [Baixar o SDK de visualização e atualizar o Framework]
3. [Configurar os dados básicos]
4. [Definir o modelo de dados básicos]
5. [Inicializar e usar o contexto de sincronização e a tabela de sincronização]
6. [Testar o aplicativo]

## <a name="get-app"></a>Obter a amostra de aplicativo de início rápido

Siga as instruções contidas em [Introdução aos serviços móveis] e baixe o projeto de início rápido.

## <a name="update-app"></a>Baixar o SDK de visualização e atualizar o framework

1. Para adicionar o suporte offline para nosso aplicativo, vamos obter uma versão do SDK do iOS para Serviços Móveis que dá suporte à sincronização offline. Como estamos lançando o aplicativo como um recurso de visualização, ele ainda não está disponível oficialmente para ser baixado no SDK. [Baixe o SDK de visualização aqui].

2. Em seguida, remova a referência **WindowsAzureMobileServices.framework** existente do projeto no Xcode selecionando-a, clicando no menu **Editar** e selecionando "Mover para Lixeira" para realmente excluir os arquivos.

      ![][update-framework-1]

3. Descompacte o conteúdo do novo SDK de visualização e, no lugar do SDK antigo, arraste e solte o novo SDK do **WindowsAzureMobileServices.framework**. Certifique-se de que a opção "Copiar itens na pasta do grupo de destino (se necessário)" está marcada.

      ![][update-framework-2]


## <a name="setup-core-data"></a>Configurar os dados básicos

1. O SDK dos Serviços Móveis do iOS permite usar qualquer armazenamento persistente, desde que esteja em conformidade com o protocolo **MSSyncContextDataSource**. Incluído no SDK é uma fonte de dados implementar esse protocolo com base em [dados básicos].

2. Como o aplicativo usa dados básicos, navegue até **Destinos** --> **Fases de compilação** e, sob **Vincular binário com bibliotecas**, adicione **CoreData.framework**.

      ![][core-data-1]

      ![][core-data-2]

3. Estamos adicionando dados básicos a um projeto existente no Xcode que não dá suporte a dados básicos. Por isso, precisamos adicionar códigos clichê adicionais a várias partes do projeto. Primeiro, adicione o seguinte código em **QSAppDelegate.h**:

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. Em seguida, substitua o conteúdo de **QSAppDelegate.m** pelo código a seguir. Esse é quase o mesmo código que você obtém quando você cria um novo aplicativo no Xcode e marca a caixa de seleção "Usar Dados Básicos", exceto que você está usando um tipo de simultaneidade de fila particular ao inicializar o **_managedObjectContext**. Com essa alteração você está quase pronto para usar dados básicos, porém, você não está fazendo nada com eles ainda.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Definir o modelo de dados básicos

1. Vamos continuar a configurar o aplicativo com dados básicos, definindo o modelo de dados. Não começaremos usando esse modelo de dados ainda. Primeiro, vamos definir o esquema ou modelo de dados principal. Para começar, clique em **Arquivo** -> **Novo Arquivo** e selecione **Modelo de Dados** na seção **Dados Básicos**. Quando for solicitado um nome de arquivo, use **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. Em seguida, vamos definir as entidades (tabelas) de que realmente precisamos. Criaremos três tabelas (entidades) usando o editor de modelo de Dados Básicos. Para obter mais informações, consulte a [Ajuda do Editor de Modelo de Dados Básicos].

  * TodoItem: Para armazenar os próprios itens
  * MS_TableOperations: Para acompanhar os itens que devem ser sincronizados com o servidor (necessário para o recurso offline funcionar)
  * MS_TableOperationErrors: Para acompanhar quaisquer eventuais erros que ocorram durante a sincronização offline (necessário para o recurso offline funcionar)

      ![][defining-core-data-model-editor]

3. Defina as três entidades conforme mostrado abaixo. Salve o modelo e compile o projeto para certificar-se de que tudo está em ordem. Agora terminamos de configurar o aplicativo para trabalhar com Dados Básicos, mas o aplicativo não está usando esses dados.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Atributo  |  Tipo   |
    |----------- |  ------ |
    | id         | Cadeia de caracteres  |
    | complete   | Booleano |
    | text       | Cadeia de caracteres  |
    | ms_version | Cadeia de caracteres  |

    **MS_TableOperations**

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Inteiro de 64 bits  |
    | properties | Dados binários |
    | itemId     | Cadeia de caracteres      |
    | table      | Cadeia de caracteres      |

    **MS_TableOperationErrors**

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Cadeia de caracteres      |
    | properties | Dados binários |

## <a name="setup-sync"></a> Inicializar e usar o contexto de sincronização e a tabela de sincronização

1. Para iniciar o armazenamento em cache de dados offline, vamos substituir a utilização de **MSTable** por **MSSyncTable** para acessar o serviço móvel. Ao contrário de um **MSTable** comum, uma tabela de sincronização é como uma tabela local que acrescenta a capacidade de enviar as alterações feitas localmente a uma tabela remota e de efetuar pull dessas alterações localmente. Em **QSTodoService.h**, remova a definição da propriedade **table**:

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. No **QSTodoService.m**, remova as duas linhas a seguir de **init**:

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. Depois disso, novamente em **QSTodoService.m**, vamos inicializar o contexto de sincronização no **MSClient** com a implementação de armazenamento de dados com base em Dados Básicos acima. O contexto é responsável por acompanhar quais itens foram alterados localmente, além de enviá-los para o servidor quando uma operação de envio é iniciada. Para inicializar o contexto precisamos de uma fonte de dados (a implementação **MSCoreDataStore** do protocolo) e uma implementação de **MSSyncContextDelegate** opcional. Insira essas linhas imediatamente sobre as duas linhas inseridas acima.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. Em seguida, vamos atualizar as operações em **QSTodoService.m** para usar a tabela de sincronização em vez da tabela normal. Em primeiro lugar, substitua **refreshDataOnSuccess** pela implementação a seguir. Isso recupera dados do serviço, portanto, vamos atualizá-lo para usar uma tabela de sincronização, solicitar à tabela de sincronização para efetuar pull apenas dos itens que correspondam a nossos critérios e começar a carregar dados da tabela de sincronização local para a propriedade **items** do serviço. Com esse código,  **refreshDataOnSuccess** efetua pull dos dados da tabela remota na tabela local (sincronização). Geralmente, devemos efetuar pull somente de um subconjunto da tabela, para não sobrecarregar o cliente com informações desnecessárias.

    Para isso e para as operações restantes mais abaixo, reunimos as chamadas para os blocos de conclusão em uma chamada **dispatch_async**, para o thread principal. Quando inicializamos o contexto de sincronização, não passamos um parâmetro de retorno de chamada, então o framework cria uma fila serial padrão que envia os resultados de todas as operações syncTable para um thread em segundo plano. Ao modificar os componentes de interface do usuário, precisamos expedir o código de volta para o thread de interface do usuário.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Em seguida, substitua **addItem** no **QSTodoService.m**, como demonstrado a seguir. Com essa alteração, você está colocando a operação em fila para enviar as alterações por push para o serviço remoto e torná-las visíveis para todos:

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Atualize **completeItem** no **QSTodoService.m**, como demonstrado a seguir. Ao contrário do que ocorre em **MSTable**, o bloco de conclusão da operação **update** para **MSSyncTable** não tem nenhum item atualizado. Com **MSTable**, o servidor modifica o item que está sendo atualizado e essa modificação é refletida no cliente. Com **MSSyncTable**, os itens atualizados não são modificados e o bloco de conclusão não tem um parâmetro.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Adicione a seguinte declaração de operação de **syncData** para **QSTodoService.h**:

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. Novamente em **QSTodoListViewController.m**, substitua a chamada para **[self refresh]** no final da operação **viewDidLoad** pelo seguinte código:

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. Agora, vamos testar de verdade o aplicativo offline. Adicione alguns itens ao aplicativo e, em seguida, visite o Portal de Gerenciamento do Azure e observe a guia **Dados** para seu aplicativo. Você verá que nenhum item foi adicionado ainda.

12. Em seguida, execute o gesto de atualização no aplicativo, arrastando-o de cima para baixo. Em seguida, visite o Portal de Gerenciamento do Azure novamente e examine outra vez a guia **Dados**. Agora, você verá os dados salvos na nuvem. Você também pode fechar o aplicativo após a adição de um item (ou edição, se o aplicativo tem a funcionalidade de edição de itens habilitada). Quando o aplicativo é reinicializado, ele sincronizará com o servidor e salvará as alterações.

13. Quando o cliente faz algumas alterações aos itens localmente, essas alterações são armazenadas no contexto de sincronização a ser enviado ao servidor. Uma operação de envio por *push* envia as alterações sob acompanhamento para o servidor remoto, mas aqui, não temos nenhuma chamada de push para o servidor. No entanto, *antes de um pull ser realizado, quaisquer eventuais operações pendentes são geralmente enviadas por push ao servidor*, portanto, um envio por push ainda está acontecendo automaticamente evitar conflitos. Esse é o motivo de não existir nenhuma chamada explícita para envio por *push* neste aplicativo.

## <a name="test-app"></a>Testar o aplicativo

Por fim, vamos testar o aplicativo offline. Adicione alguns itens ao aplicativo. Em seguida, acesse o portal e procure os dados (ou use uma ferramenta de rede como o PostMan ou o Fiddler para consultar a tabela diretamente).

Você verá que os itens ainda não foram adicionados ao serviço. Em seguida, execute o gesto de atualização no aplicativo, arrastando-o de cima para baixo. Agora, você verá que os dados foram salvos na nuvem. Você pode até mesmo fechar o aplicativo após a adição de alguns itens. Quando você iniciar o aplicativo novamente, ele será sincronizado com o servidor e as alterações serão salvas.

## Próximas etapas

* [Tratando conflitos com o suporte offline para Serviços Móveis]

<!-- URLs. -->

[Obter a amostra de aplicativo de início rápido]: #get-app
[Baixar o SDK de visualização e atualizar o Framework]: #update-app
[Configurar os dados básicos]: #setup-core-data
[Definir o modelo de dados básicos]: #defining-core-data
[Inicializar e usar o contexto de sincronização e a tabela de sincronização]: #setup-sync
[Testar o aplicativo]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Ajuda do Editor de Modelo de Dados Básicos]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Criar Uma Conexão de Saída]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Criar uma interface do usuário]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adicionando uma Transição Entre Cenas em um Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adicionando uma Cena a um Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Dados Básicos]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Baixe o SDK de visualização aqui]: http://aka.ms/Gc6fex
[Como usar a biblioteca de cliente de Serviços Móveis para iOS]: /pt-br/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Introdução à amostra de iOS Offline]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-ios-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-ios-get-started-data/
[Tratando conflitos com o suporte offline para Serviços Móveis]: /pt-br/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
