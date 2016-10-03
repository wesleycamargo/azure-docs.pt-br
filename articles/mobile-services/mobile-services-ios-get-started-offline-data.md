<properties
	pageTitle="Introdução à sincronização de dados Offline nos Serviços Móveis (iOS) | Microsoft Azure"
	description="Saiba como usar serviços móveis do Azure para cache e sincronização de dados offline no seu aplicativo iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="krisragh;donnam"/>

# Introdução à sincronização de dados offline nos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Habilitar sincronização offline para seu aplicativo móvel iOS](../app-service-mobile/app-service-mobile-ios-get-started-offline-data.md).

A sincronização offline permite exibir, adicionar ou modificar dados em um aplicativo móvel mesmo quando não há nenhuma conexão de rede. Neste tutorial, você aprenderá como o seu aplicativo pode armazenar alterações em um banco de dados offline automaticamente e sincronizar as alterações sempre que estiver online novamente.

A sincronização offline oferece várias vantagens:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Torna os aplicativos resilientes em relação à conectividade da rede intermitente
* Permite criar e modificar dados mesmo com pouca ou nenhuma conectividade
* Sincroniza dados em vários dispositivos
* Detecta os conflitos quando o mesmo registro é modificado por dois dispositivos

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se não tiver uma conta, você poderá se inscrever para uma avaliação do Azure e obter [serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação](https://azure.microsoft.com/pricing/details/mobile-services/). Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target=“\_blank”).

Este tutorial se baseia no [tutorial de Início Rápido dos Serviços Móveis], que você deve concluir primeiro. Vamos primeiro examinar o código relacionado à sincronização offline já no Início Rápido.

## <a name="review-sync"></a>Examinar o código de sincronização de Serviços Móveis

A Sincronização offline dos Serviços móveis do Azure permite aos usuários finais interagir com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, inicialize o contexto de sincronização de `MSClient` e faça referência a um repositório local. Faça referência à sua tabela através da interface `MSSyncTable`.

* Em **Qstodoservice**, observe que o tipo do membro `syncTable` é `MSSyncTable`. A sincronização offline usa esse em vez de `MSTable`. Quando uma tabela de sincronização é usada, todas as operações vão para o armazenamento local e somente são sincronizadas com o serviço remoto com operações de push e pull explícitas.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Para obter uma referência a uma tabela de sincronização, use o método `syncTableWithName`. Para remover a funcionalidade de sincronização offline, use `tableWithName` em vez disso.

* Em **Qstodoservice**, antes de realizar operações de tabela, o armazenamento local é inicializado em `QSTodoService.init`:

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

Isso cria um repositório local usando a interface `MSCoreDataStore`. Você pode fornecer um repositório local diferente implementando o protocolo `MSSyncContextDataSource`.

O primeiro parâmetro de `initWithDelegate` especifica um manipulador de conflito, mas uma vez passado o `nil`, obtemos o manipulador de conflito padrão que falha em qualquer conflito. Para obter detalhes sobre como implementar um manipulador de conflitos personalizado, confira o tutorial [Tratando conflitos com o suporte offline para os Serviços Móveis]

* Em **Qstodoservice**, `syncData` primeiro envia novas alterações por push e chama `pullData` para obter dados do serviço remoto. Em `syncData`, primeiro chamamos `pushWithCompletion` no contexto de sincronização. Esse método é um membro de `MSSyncContext` -- em vez da tabela de sincronização em si -- porque envia por push as alterações para todas as tabelas. Somente os registros que são modificados de alguma forma localmente, por meio de operações de criação, de atualização ou de exclusão, são enviados ao servidor. No final da `syncData`, podemos chamar o auxiliar `pullData`.

Observe que, nesse exemplo, a operação de envio por push não é totalmente necessária. Se houver alterações pendentes no contexto de sincronização para a tabela que está executando uma operação de push, um pull sempre emite um push primeiro. No entanto, se você tiver mais de uma tabela de sincronização, chame o push explicitamente para ter consistência entre tabelas.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* Em **Qstodoservice.m**, `pullData` obtém novos dados que correspondem a uma consulta. `pullData` cama `MSSyncTable.pullWithQuery` para recuperar dados remotos e armazená-los localmente. `pullWithQuery` também permite que você especifique uma consulta para filtrar os registros que deseja recuperar. Nesse exemplo, a consulta recupera apenas todos os registros na tabela remota `TodoItem`.

O segundo parâmetro para `pullWithQuery` é uma ID de consulta _ para _sincronização incremental. A sincronização incremental recupera somente os registros modificados desde a última sincronização, usando o carimbo de data/hora do registro `UpdatedAt`, chamado de `ms_updatedAt` no armazenamento local. A ID da consulta é uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `nil` como a ID da consulta. Isso não funciona porque ele irá recuperar todos os registros em cada operação de pull.

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE] Para remover registros do armazenamento local do dispositivo quando tiverem sido excluídos do banco de dados do serviço móvel, você deve habilitar a [Exclusão Reversível]. Caso contrário, seu aplicativo deverá chamar `MSSyncTable.purgeWithQuery` periodicamente para limpar o armazenamento local.


* Em **QSTodoService.m**, os métodos `addItem` e `completeItem` invocam `syncData` após a modificação de dados. Em **QSTodoListViewController.m**, o método `refresh` também chama `syncData` para que a interface do usuário exiba os dados mais recentes em cada atualização e no lançamento (`init` chama `refresh`).

Visto que o aplicativo chama `syncData` sempre que você modifica os dados, o aplicativo pressupõe que você está online toda vez que edita dados no aplicativo.

## <a name="review-core-data"></a>Examinar o modelo de dados principais

Ao usar o armazenamento offline de dados principais, você precisa definir determinadas tabelas e campos em seu modelo de dados. O aplicativo de exemplo já inclui um modelo de dados com o formato correto. Nesta seção percorreremos essas tabelas e veremos como elas são usadas.

- Abra **QSDataModel.xcdatamodeld**. Há quatro tabelas definidas: três que são usadas pelo SDK e uma para os próprios itens pendentes:

      * MS\_TableOperations: para acompanhar os itens que serão sincronizados com o servidor
      * MS\_TableOperationErrors: para acompanhar os erros que ocorrerem durante a sincronização offline
      * MS\_TableConfig: para controlar a hora da última atualização da última operação de sincronização para todas as operações de pull
      * TodoItem: para armazenar os itens pendentes. As colunas do sistema **ms\_createdAt**, **ms\_updatedAt** e **ms\_version** são propriedades opcionais do sistema.

>[AZURE.NOTE] O SDK dos serviços móveis reserva nomes de coluna que começam com “**`ms_`**”. Não use esse prefixo em algo diferente das colunas do sistema. Caso contrário, os nomes de coluna serão modificados ao usar o serviço remoto.

- Ao usar o recurso de sincronização offline, você deve definir as tabelas do sistema, conforme mostrado abaixo.

    ### Tabelas do sistema

    #### MS\_TableOperations

    | Atributo | Tipo |
    |-------------- |   ------    |
    | ID (obrigatório) | Número Inteiro 64 |
    | itemId | Cadeia de caracteres |
    | propriedades | Dados binários |
    | tabela | Cadeia de caracteres |
    | tableKind | Número inteiro 16 |

    #### MS\_TableOperationErrors

    | Atributo | Tipo |
    |-------------- | ----------  |
    | ID (obrigatório) | Cadeia de caracteres |
    | operationId | Número Inteiro 64 |
    | propriedades | Dados binários |
    | tableKind | Número inteiro 16 |

    #### MS\_TableConfig


    | Atributo | Tipo |
    |-------------- | ----------  |
    | ID (obrigatório) | Cadeia de caracteres |
    | chave | Cadeia de caracteres |
    | keyType | Número Inteiro 64 |
    | tabela | Cadeia de caracteres |
    | valor | Cadeia de caracteres |

    ### Tabela de Dados

    #### TodoItem

    | Atributo | Tipo | Observação |
    |-------------- |  ------ | -------------------------------------------------------|
    | ID (obrigatório) | Cadeia de caracteres | chave primária no repositório remoto (obrigatório) |
    | concluído | Booliano | campo de item de tarefa |
    | texto | Cadeia de caracteres | campo de item de tarefa |
    | ms\_createdAt | Data | (opcional) é mapeado para a propriedade do sistema \_\_createdAt |
    | ms\_updatedAt | Data | (opcional) é mapeado para a propriedade do sistema \_updatedAt |
    | ms\_version | Cadeia de caracteres | (opcional) usado para detectar conflitos, é mapeado para \_version |



## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para que ele não sincronize ao inicializar, ou ao inserir e atualizar itens, mas somente quando o botão do gesto de atualização for executado.

* Em **QSTodoListViewController.m**, altere `viewDidLoad` para remover a chamada para `[self refresh]` no final do método. Os dados, em vez de sincronizados com o servidor na inicialização do aplicativo, se tornarão o conteúdo do repositório local.

* Em **QSTodoService.m**, modifique `addItem` para que ele não sincronize após o item ser inserido. Remova o bloco `self syncData` e substitua-o pelo seguinte:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* Da mesma forma, novamente em **QSTodoService.m**, em `completeItem`, remova o bloco para `self syncData` e substitua pelo seguinte:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Testar o aplicativo

Nesta seção, você desligará o Wi-Fi no simulador para criar um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no armazenamento local de dados principais, mas não sincronizados com o serviço móvel.

1. Desabilitar a conexão com a internet em seu Mac. A desativação do WiFi somente no simulador de iOS pode não ter efeito, pois o simulador ainda pode usar a conexão com a internet do Mac host; assim, desabilite a internet do computador em si. Isso simula um cenário offline.

2. Adicione alguns itens ToDo ou complete alguns itens. Feche o simulador (ou feche forçadamente o aplicativo) e reinicie. Verifique se suas mudanças foram mantidas. Observe que os itens de dados ainda são exibidos porque eles são mantidos no repositório de dados principais local.

3. Exiba o conteúdo da tabela remota TodoItem: Verifique se os novos itens _não_ foram sincronizados com o servidor.

   - Para o back-end do JavaScript, vá para o [Portal clássico do Azure](http://manage.windowsazure.com) e clique na guia Dados para exibir o conteúdo da tabela `TodoItem`.
   - Para o back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL como o SQL Server Management Studio, ou então com um cliente REST, como o Fiddler ou Postman.

4. Ligue o Wi-Fi no simulador de iOS. Faça o gesto de atualização puxando a lista de itens para baixo. Você verá um mostrador giratório de progresso e o texto "Sincronizando...".

5. Exiba os dados de TodoItem novamente. Os TodoItems novos e modificados agora devem aparecer.

## Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, você usou a interface `MSSyncTable` e inicializou `MSClient.syncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados baseado em dados principais.

Ao usar um repositório local de dados principais, você deve definir várias tabelas com as [propriedades do sistema corretas][Review the Core Data model]. As operações normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Para sincronizar o repositório local com o servidor, você usou `MSSyncTable.pullWithQuery` e `MSClient.syncContext.pushWithCompletion`:

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Próximas etapas

* [Tratando conflitos com o suporte offline para os Serviços Móveis]

* [Usando a exclusão reversível nos Serviços Móveis][Soft Delete]

## Recursos adicionais

* [Cloud Cover: sincronização offline em serviços móveis do Azure]

* [Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure] \(observação: demonstrações são para Windows, mas a discussão dos recursos se aplica a todas as plataformas)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Tratando conflitos com o suporte offline para os Serviços Móveis]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[Exclusão Reversível]: mobile-services-using-soft-delete.md

[Cloud Cover: sincronização offline em serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[tutorial de Início Rápido dos Serviços Móveis]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0727_2016-->