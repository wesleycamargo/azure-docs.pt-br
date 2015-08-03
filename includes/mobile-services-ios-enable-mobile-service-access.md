
Agora, vamos atualizar o aplicativo para armazenar itens nos Serviços Móveis do Azure, em vez da coleção na memória local.

* Em **TodoService.h**, localize a seguinte linha:

```
// TODO - create an MSClient property
```

Substitua este comentário com a seguinte linha. Isso cria uma propriedade que representa o `MSClient` que se conecta ao serviço

```
@property (nonatomic, strong)   MSClient *client;
```


* Em **Todoservice**, localize a seguinte linha:

```
// TODO - create an MSTable property for your items
```

Substitua este comentário com a seguinte linha dentro da declaração `@interface`. Isso cria uma representação de propriedade para a tabela de serviços móveis.

```
@property (nonatomic, strong)   MSTable *table;
```


* No Portal de Gerenciamento, clique em **Serviços Móveis**, em seguida, clique no serviço móvel. Clique na guia **Painel** e anote a **URL do Site**. Em seguida, clique em **Gerenciar Chaves** e anote a **Chave do Aplicativo**. Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.


* Em **Todoservice**, localize a seguinte linha:

```
// Initialize the Mobile Service client with your URL and key.
```

Após esse comentário, adicione a seguinte linha de código: Substitua `APPURL` e `APPKEY` com a URL do site e a aplicação da chave você obteve na etapa anterior.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* Em **Todoservice.m**, localize a seguinte linha:

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Substitua este comentário com a seguinte linha. Isso cria a instância da tabela TodoItem.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* Em **Todoservice.m**, localize a seguinte linha:

```
// Create a predicate that finds items where complete is false
```

Substitua este comentário com a seguinte linha. Isso cria uma consulta para retornar todas as tarefas que ainda não foram concluídas.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Localize a seguinte linha:

```
// Query the TodoItem table and update the items property with the results from the service
```

Substitua o comentário e a invocação de bloco **completion** subsequente pelo seguinte código:

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Localize o método **addItem** e substitua o seu corpo pelo código a seguir. Esse código envia uma solicitação de inserção ao serviço móvel.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* Localize o método **completeItem** e localize a seguinte linha de código comentada:

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Substitua o corpo do método, daqui para o final do método, pelo código a seguir: Este código remove os itens de tarefas depois que eles estiverem marcados como concluídos.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* Em TodoListController.m, localize o método **onAdd** e substitua-o pelo código a seguir:

```
- (IBAction)onAdd:(id)sender
{
    if (itemText.text.length  == 0)
    {
        return;
    }

    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
    UITableView *view = self.tableView;
    [self.todoService addItem:item completion:^(NSUInteger index)
    {
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        [view insertRowsAtIndexPaths:@[ indexPath ]
                    withRowAnimation:UITableViewRowAnimationTop];
    }];

    itemText.text = @"";
}
```

<!---HONumber=July15_HO4-->