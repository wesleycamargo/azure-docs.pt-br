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
	ms.date="08/01/2016"
	ms.author="krisragh"/>

# Habilitar sincronização offline para seu aplicativo móvel iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral

Este tutorial aborda o recurso de sincronização offline de Aplicativos móveis do Azure para iOS. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações serão sincronizadas ao serviço remoto.

Se essa for sua primeira experiência com os Aplicativos Móveis do Azure, você deve primeiro concluir o tutorial [Criar um aplicativo do iOS]. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## <a name="review-sync"></a>Examine o código de sincronização do cliente

O projeto de cliente que você baixou para o tutorial [Criar um aplicativo iOS] já contém a sincronização offline do código de suporte usando um banco de dados local baseado em dados básicos. Esta seção é um resumo do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].

O recurso de sincronização de dados offline dos aplicativos móveis do Azure permite aos usuários finais interagir com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, inicialize o contexto de sincronização de `MSClient` e faça referência a um repositório local. Em seguida, faça referência à sua tabela por meio da interface da `MSSyncTable`.

1. Em **QSTodoService** (Objective-C) ou **ToDoTableViewController.swift** (Swift), observe que o tipo do membro `syncTable` é `MSSyncTable`. A sincronização offline usa essa interface de tabela de sincronização em vez de `MSTable`. Quando uma tabela de sincronização é usada, todas as operações vão para o repositório local e só são sincronizadas com o back-end remoto com operações de push e pull explícitas.

    Para obter uma referência a uma tabela de sincronização, use o método `syncTableWithName` em `MSClient`. Para remover a funcionalidade de sincronização offline, use `tableWithName` em vez disso.

2. Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. Aqui está o código relevante.
	
	**Objective-C**:
	
	No método `QSTodoService.init`:
	
	
	        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
	        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
	
	
	**Swift**:
	
	No método `ToDoTableViewController.viewDidLoad`:
	
	
	        let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
	        let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
	        self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
	        client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
	

	Isso cria um repositório local usando a interface `MSCoreDataStore`, que é fornecida no SDK do aplicativo móvel. Em vez disso, é possível fornecer um repositório local diferente implementando o protocolo `MSSyncContextDataSource`.
	
	Além disso, o primeiro parâmetro de `MSSyncContext` é usado para especificar um manipulador de conflito. Já que passamos `nil`, obteremos o manipulador de conflito padrão, que falha em qualquer conflito.
	
3. Agora, vamos executar a operação de sincronização em si e obter dados de back-end remoto.

	**Objective-C**:
	
	`syncData` primeiro envia novas alterações por push e chama `pullData` para obter dados do back-end remoto. Por sua vez, o método `pullData` obtém novos dados que correspondem a uma consulta:
	
	
	        -(void)syncData:(QSCompletionBlock)completion
	        {
	            // push all changes in the sync context, then pull new data
	            [self.client.syncContext pushWithCompletion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	                [self pullData:completion];
	            }];
	        }
	
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
        
        
      **Swift**:
        
        
		func onRefresh(sender: UIRefreshControl!) {
		    UIApplication.sharedApplication().networkActivityIndicatorVisible = true
		    
		    self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
		        (error) -> Void in
		        
		        UIApplication.sharedApplication().networkActivityIndicatorVisible = false
		        
		        if error != nil {
		            // A real application would handle various errors like network conditions,
		            // server conflicts, etc via the MSSyncContextDelegate
		            print("Error: (error!.description)")
		            
		            // We will just discard our changes and keep the servers copy for simplicity
		            if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
		                for opError in opErrors {
		                    print("Attempted operation to item (opError.itemId)")
		                    if (opError.operation == .Insert || opError.operation == .Delete) {
		                        print("Insert/Delete, failed discarding changes")
		                        opError.cancelOperationAndDiscardItemWithCompletion(nil)
		                    } else {
		                        print("Update failed, reverting to server's copy")
		                        opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
		                    }
		                }
		            }
		        }
		        self.refreshControl?.endRefreshing()
		    }
		} 
	
	
	Na versão Objective-C, em `syncData`, primeiro chamamos `pushWithCompletion` no contexto de sincronização. Esse método é um membro de `MSSyncContext` (em vez da tabela de sincronização em si) porque enviará as alterações a todas as tabelas. Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor. Em seguida, o auxiliar `pullData` é chamado, o que chama `MSSyncTable.pullWithQuery` para recuperar dados remotos e armazená-los no banco de dados local.
	
	Na versão Swift, não há nenhuma chamada para `pushWithCompletion`. Isso ocorre porque a operação de envio por push não era estritamente necessária. Se houver alterações pendentes no contexto de sincronização para a tabela que está executando uma operação de push/pull, push é sempre emitido primeiro. No entanto, se você tiver mais de uma tabela de sincronização, é melhor chamar explicitamente por push para garantir que tudo seja consistente por todas as tabelas relacionadas.
	
	Em ambas as versões Objective-C e Swift, o método `pullWithQuery` permite que você especifique uma consulta para filtrar os registros que deseja recuperar. Neste exemplo, a consulta recupera apenas todos os registros na tabela remota `TodoItem`.
	
	O segundo parâmetro para `pullWithQuery` é uma ID de consulta que é usada para *sincronização incremental*. A sincronização incremental recupera somente os registros modificados desde a última sincronização, usando o carimbo de data/hora do registro `UpdatedAt` (chamado de `updatedAt` no repositório local). A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `nil` como a ID da consulta. Observe que isso pode ser potencialmente ineficiente, já que irá recuperar todos os registros de cada operação de recepção.

5. O aplicativo Objective-C é sincronizado quando modificamos ou adicionamos dados, um usuário executa o gesto de atualização e na inicialização. O aplicativo Swift é sincronizado quando um usuário executa o gesto de atualização e na inicialização.

Como o aplicativo é sincronizado sempre que dados são modificados (Objective-C) ou sempre que o aplicativo é iniciado (Objective-C & Swift), o aplicativo pressupõe que o usuário está online. Em outra seção, atualizaremos o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## <a name="review-core-data"></a>Examinar o modelo de dados principais

Ao usar o armazenamento offline de dados principais, você precisa definir determinadas tabelas e campos em seu modelo de dados. O aplicativo de exemplo já inclui um modelo de dados com o formato correto. Nesta seção observaremos essas tabelas e como elas são usadas.

- Abra **QSDataModel.xcdatamodeld**. Há quatro tabelas definidas - três que são usados pelo SDK e uma tabela para os itens ToDo:
      * MS\_TableOperations: Para acompanhar os itens que devem ser sincronizados com o servidor
      * MS\_TableOperationErrors: para acompanhar todos os erros que ocorrerem durante a sincronização offline
      * MS\_TableConfig: para controlar a hora da última atualização para a última operação de sincronização para todas as operações de recepção
      * TodoItem: para armazenar os itens de tarefas. As colunas do sistema **createdAt**, **updatedAt** e **version** são propriedades opcionais do sistema.

>[AZURE.NOTE] O SDK de aplicativos móveis do Azure reserva nomes de coluna que começam com "**``**". Você não deve usar este prefixo em nada exceto colunas do sistema, caso contrário, os nomes de coluna serão modificados ao usar o back-end remoto.

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
    | value | Cadeia de caracteres |

    ### Tabela de dados

    **TodoItem**

    | Atributo | Tipo | Observação |
    |-----------   |  ------ | -------------------------------------------------------|
    | ID | Cadeia de caracteres, marcadas como obrigatórias | chave primária no repositório remoto |
    | concluído | Booliano | campo de item de tarefa |
    | texto | Cadeia de caracteres | campo de item de tarefa |
    | createdAt | Data | (opcional) é mapeado para a propriedade do sistema createdAt |
    | updatedAt | Data | (opcional) é mapeado para a propriedade do sistema updatedAt |
    | version | Cadeia de caracteres | (opcional) usado para detectar conflitos, é mapeado para version |


## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para que ele não sincronize ao inicializar o aplicativo, ou ao inserir e atualizar itens, mas somente quando o botão do gesto de atualização for executado.

**Objective-C**:

1. Em **QSTodoListViewController.m**, altere o método **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora, os dados não serão sincronizados com o servidor na inicialização do aplicativo, mas em vez disso, se tornarão o conteúdo do repositório local.

2. Em **QSTodoService.m**, modifique a definição de `addItem` para que não ocorra sincronização após o item ser inserido. Remova o bloco `self syncData` e substitua-o pelo seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifique a definição de `completeItem` conforme explicado acima; remova o bloco para `self syncData` e substitua-o pelo seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. Em `viewDidLoad` no **ToDoTableViewController.swift**, comente essas duas linhas para parar a sincronização na inicialização do aplicativo. No momento da redação deste artigo, o aplicativo Todo Swift não atualiza o serviço quando alguém adiciona ou conclui um item, somente na inicialização do aplicativo.

		self.refreshControl?.beginRefreshing()
		self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Testar o aplicativo

Nesta seção, você se conecta a uma URL inválida para simular um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no repositório local de dados principais, mas não sincronizados com o back-end móvel.

1. Altere a URL do aplicativo móvel em **Qstodoservice** para uma URL inválida e execute novamente o aplicativo:

	**Objective-C** em QSTodoService.m:
	
        	self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
	
	**Swift** em ToDoTableViewController.swift:

		let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Adicione alguns itens ToDo. Feche o simulador (ou feche forçadamente o aplicativo) e reinicie. Verifique se suas mudanças foram mantidas.

3. Exiba o conteúdo da tabela TodoItem remota:

    + Para um back-end Node.js, vá para o [Portal do Azure](https://portal.azure.com/) e no back-end do aplicativo móvel clique em **Tabelas fáceis** > **TodoItem** para exibir o conteúdo da tabela `TodoItem`.
   	+ Para um back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL como o SQL Server Management Studio, ou então com um cliente REST, como o Fiddler ou Postman.

    Confirme que os novos itens *não* tenham sido sincronizados com o servidor:

4. Altere a URL de vola para a correta em **QSTodoService.m** e execute novamente o aplicativo. Faça o gesto de atualização puxando a lista de itens para baixo. Você verá um controle giratório de andamento.

5. Exiba os dados de TodoItem novamente. Os TodoItems novos e modificados agora devem aparecer.

## Resumo

Para dar suporte a esse recurso de sincronização offline, usamos a interface `MSSyncTable` e inicializamos `MSClient.syncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados baseado em dados principais.

Ao usar um repositório local de dados principais, você deve definir várias tabelas com as [propriedades do sistema corretas](#review-core-data).

As operações CRUD normais para os Aplicativos móveis do Azure funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o repositório local com o servidor, usamos o método `MSSyncTable.pullWithQuery`.


## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] (observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)

<!-- URLs. -->


[Criar um aplicativo do iOS]: ../app-service-mobile-ios-get-started.md
[Criar um aplicativo iOS]: ../app-service-mobile-ios-get-started.md
[Sincronização de dados offline em Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0810_2016-->