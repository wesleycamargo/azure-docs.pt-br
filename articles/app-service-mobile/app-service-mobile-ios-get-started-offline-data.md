<properties
	pageTitle="Habilitar sincronização offline para o seu Aplicativo móvel do Azure (iOS)"
	description="Aprenda a usar os aplicativos móveis do Serviço de Aplicativo para cache e sincronização de dados offline no aplicativo iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="krisragh"/>

# Habilitar sincronização offline para seu aplicativo móvel iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Visão geral

Este tutorial aborda o recurso de sincronização offline de Aplicativos móveis do Azure para iOS. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações serão sincronizadas ao serviço remoto.

Se essa for sua primeira experiência com os Aplicativos Móveis do Azure, você deve primeiro concluir o tutorial [Criar um aplicativo do iOS]. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## <a name="review-sync"></a>Examine o código de sincronização do cliente 

O projeto de cliente que você baixou para o tutorial [Criar um aplicativo do iOS] já contém códigos que dão suporte à sincronização offline usando um banco de dados local baseado em Dados Básicos. Esta seção é um resumo do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].

O recurso de sincronização de dados offline dos aplicativos móveis do Azure permite aos usuários finais interagir com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, inicialize o contexto de sincronização de `MSClient` e faça referência a um repositório local. Em seguida, faça referência à sua tabela por meio da interface da `MSSyncTable`.

1. Em **QSTodoService.m**, observe que o tipo do membro `syncTable` é `MSSyncTable`. A sincronização offline usa essa interface de tabela de sincronização, em vez de `MSTable`. Quando uma tabela de sincronização é usada, todas as operações vão para o repositório local e só são sincronizadas com o back-end remoto com operações de push e pull explícitas.

    Para obter uma referência a uma tabela de sincronização, use o método `syncTableWithName`. Para remover a funcionalidade de sincronização offline, use `tableWithName` em vez disso.

2. Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. Este é o código relevante no método `QSTodoService.init`:

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    Isso cria um repositório local usando a interface `MSCoreDataStore`, que é fornecida no SDK do aplicativo móvel. Em vez disso, é possível fornecer um repositório local diferente implementando o protocolo `MSSyncContextDataSource`.

    O primeiro parâmetro de `initWithDelegate` é usado para especificar um manipulador de conflito. Já que passamos `nil`, obteremos o manipulador de conflito padrão, que falha em qualquer conflito.

	<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. Os métodos `pullData` e `syncData` realizam a operação de sincronização em si: `syncData` primeiro envia novas alterações, então chama `pullData` para obter dados de back-end remoto.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    Por sua vez, o método `pullData` obtém novos dados que correspondem a uma consulta:

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

    Em `syncData`, primeiro chamamos `pushWithCompletion` no contexto de sincronização. Esse método é um membro de `MSSyncContext` (em vez da tabela de sincronização em si) porque enviará as alterações a todas as tabelas. Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor. Em seguida, o auxiliar `pullData` é chamado, o que chama `MSSyncTable.pullWithQuery` para recuperar dados remotos e armazená-los no banco de dados local.

    Observe que, neste exemplo, a operação de envio não era estritamente necessária. Se houver alterações pendentes no contexto de sincronização para a tabela que está executando uma operação de push/pull, push é sempre emitido primeiro. No entanto, se você tiver mais de uma tabela de sincronização, é melhor chamar explicitamente por push para garantir que tudo seja consistente por todas as tabelas relacionadas.

    O método `pullWithQuery` permite que você especifique uma consulta para filtrar os registros que deseja recuperar. Neste exemplo, a consulta recupera apenas todos os registros na tabela remota `TodoItem`.

    O segundo parâmetro para `pullWithQuery` é uma ID de consulta que é usada para *sincronização incremental*. A sincronização incremental recupera somente os registros modificados desde a última sincronização, usando o carimbo de data/hora `UpdatedAt` do registro (chamado `ms_updatedAt` no repositório local). A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `nil` como a ID da consulta. Observe que isso pode ser potencialmente ineficiente, já que irá recuperar todos os registros de cada operação de recepção.

	<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to purge the local store.
 -->

5. Na classe `QSTodoService`, o método `syncData` é chamado após as operações que modificam dados, `addItem` e `completeItem`. Ele também é chamado por meio de `QSTodoListViewController.refresh`, de modo que o usuário obtém os dados mais recentes sempre que executar o gesto de atualização. O aplicativo também realiza uma sincronização na inicialização, já que `QSTodoListViewController.init` chama `refresh`.

    Como `syncData` é chamado sempre que dados são modificados, esse aplicativo presume que o usuário está online sempre que este usuário está editando dados. Em outra seção, atualizaremos o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## <a name="review-core-data"></a>Examinar o modelo de dados principais

Ao usar o armazenamento offline de dados principais, você precisa definir determinadas tabelas e campos em seu modelo de dados. O aplicativo de exemplo já inclui um modelo de dados com o formato correto. Nesta seção observaremos essas tabelas e como elas são usadas.

- Abra **QSDataModel.xcdatamodeld**. Há quatro tabelas definidas - três que são usados pelo SDK e uma tabela para os itens ToDo:
      * MS\_TableOperations: Para acompanhar os itens que devem ser sincronizados com o servidor
      * MS\_TableOperationErrors: para acompanhar todos os erros que ocorrerem durante a sincronização offline
      * MS\_TableConfig: para controlar a hora da última atualização para a última operação de sincronização para todas as operações de recepção
      * TodoItem: para armazenar os itens de tarefas. As colunas do sistema **ms\_createdAt**, **ms\_updatedAt** e **ms\_version** são propriedades opcionais do sistema.

>[AZURE.NOTE]O SDK de aplicativos móveis do Azure reserva nomes de coluna que começam com “**`ms_`**”. Você não deve usar este prefixo em nada exceto colunas do sistema, caso contrário, os nomes de coluna serão modificados ao usar o back-end remoto.

- Ao usar o recurso de sincronização offline, você deve definir as tabelas do sistema, conforme mostrado abaixo.

    ### Tabelas do sistema

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | ID | Número Inteiro 64 |
    | itemId | Cadeia de caracteres |
    | propriedades | Dados binários |
    | tabela | Cadeia de caracteres |
    | tableKind | Número inteiro 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | ID | Cadeia de caracteres |
    | operationId | Número Inteiro 64 |
    | propriedades | Dados binários |
    | tableKind | Número inteiro 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | ID | Cadeia de caracteres |
    | chave | Cadeia de caracteres |
    | keyType | Número Inteiro 64 |
    | tabela | Cadeia de caracteres |
    | valor | Cadeia de caracteres |

    ### Tabela de dados

    ![][defining-core-data-todoitem-entity]

    **TodoItem**


    | Atributo | Tipo | Observação |
    |-----------   |  ------ | -------------------------------------------------------|
    | ID | Cadeia de caracteres, marcadas como obrigatórias | chave primária no repositório remoto |
    | concluído | Booliano | campo de item de tarefa |
    | texto | Cadeia de caracteres | campo de item de tarefa |
    | ms\_createdAt | Data | (opcional) é mapeado para \_\_createdAt propriedade do sistema | | ms\_updatedAt | Data | (opcional) é mapeado para \_\_updatedAt propriedade do sistema | | ms\_version | Cadeia de caracteres | (opcional) usado para detectar conflitos, é mapeado para \_\_versão |


## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para que ele não sincronize ao inicializar o aplicativo, ou ao inserir e atualizar itens, mas somente quando o botão do gesto de atualização for executado.

1. Em **QSTodoListViewController.m**, altere o método **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora, os dados não serão sincronizados com o servidor na inicialização do aplicativo, mas em vez disso, se tornarão o conteúdo do repositório local.

2. Em **QSTodoService.m**, modifique a definição de `addItem` para que não ocorra sincronização após o item ser inserido. Remova o bloco `self syncData` e substitua-o pelo seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifique a definição de `completeItem` conforme explicado acima; remova o bloco para `self syncData` e substitua-o pelo seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>Testar o aplicativo


Nesta seção, você desligará o Wi-Fi no simulador para criar um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no repositório local de dados principais, mas não sincronizados com o back-end móvel.

1. Desligue o Wi-Fi no simulador de iOS.

2. Adicione alguns itens ToDo ou complete alguns itens. Feche o simulador (ou feche forçadamente o aplicativo) e reinicie. Verifique se suas mudanças foram mantidas.

3. Exiba o conteúdo da tabela TodoItem remota:
   - Para o back-end JavaScript, vá até o Portal de Gerenciamento e clique na guia Dados para exibir o conteúdo da tabela `TodoItem`.
   - Para o back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL como o SQL Server Management Studio, ou então com um cliente REST, como o Fiddler ou Postman.

    Confirme que os novos itens *não* tenham sido sincronizados com o servidor:

4. Desligue o Wi-Fi no simulador do iOS, então faça o gesto de atualização puxando a lista de itens para baixo. Você verá um mostrador giratório de progresso e o texto "Sincronizando...".

5. Exiba os dados de TodoItem novamente. Os TodoItems novos e modificados agora devem aparecer.

## Resumo

Para dar suporte a esse recurso de sincronização offline, usamos a interface `MSSyncTable` e inicializamos `MSClient.syncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados baseado em dados principais.

Ao usar um repositório local de dados principais, você deve definir várias tabelas com as [propriedades do sistema corretas](#review-core-data).

As operações CRUD normais para os Aplicativos móveis do Azure funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `MSSyncTable.pullWithQuery` e `MSClient.syncContext.pushWithCompletion`.

*  Para executar push das mudanças no servidor, chamamos `pushWithCompletion`. Esse método é um membro de `MSSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

* Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `MSSyncTable.pullWithQuery`.

    Uma pull sempre envia um push primeiro. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.

    Observe que `pullWithQuery` pode ser usado para filtrar os dados que estão armazenados no cliente, personalizando o parâmetro `query`.

* Para habilitar a sincronização incremental, passe uma ID de consulta para `pullWithQuery`. A ID de consulta é usada para armazenar o último carimbo de data/hora atualizado dos resultados da última operação de recepção. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se a consulta tiver um parâmetro, então o mesmo valor de parâmetro deve ser parte da ID da consulta.

    Se você deseja recusar a sincronização incremental, passe `nil` como a ID da consulta. Nesse caso, todos os registros serão recuperados em cada chamada de `pullWithQuery`, que é potencialmente ineficiente.

<!-- * To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to remove records from the local database, in case they have been deleted in the remote service.
 -->

## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização Offline nos serviços móveis do Azure] (Observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)

<!-- URLs. -->


[Criar um aplicativo do iOS]: ../app-service-mobile-ios-get-started.md
[Sincronização de dados offline em Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cobertura em nuvem: sincronização Offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
 

<!---HONumber=Nov15_HO1-->