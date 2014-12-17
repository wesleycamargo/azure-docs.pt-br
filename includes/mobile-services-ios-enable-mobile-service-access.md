
Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1. Se você ainda não tiver instalado o [SDK do iOS dos Serviços Móveis](https://go.microsoft.com/fwLink/p/?LinkID=266533), instale-o agora. Uma vez instalado, copie o diretório WindowsAzureMobileServices.framework e substitua o diretório WindowsAzureMobileServices.framework incluído no projeto baixado. Dessa forma, você está usando o SDK dos Serviços Móveis do Azure mais recentes.

2. No arquivo TodoService.h, localize a seguinte linha de código comentada:

        // TODO - create an MSClient proeprty

   	Após esse comentário, adicione a seguinte linha de código:

        @property (nonatomic, strong)   MSClient *client;

   	Isso cria uma propriedade que representa o MSClient que se conecta ao serviço

3. No arquivo TodoService.m, localize a seguinte linha de código comentada:

        // TODO - create an MSTable property for your items

   	Após esse comentário, adicione a seguinte linha de código dentro da declaração @interface:

        @property (nonatomic, strong)   MSTable *table;

   	Isso cria uma representação de propriedade para a tabela de serviços móveis.

4. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

5. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

6. De volta no Xcode, abra TodoService.m e localize a seguinte linha de código comentada:

        // Initialize the Mobile Service client with your URL and key.

    Após esse comentário, adicione a seguinte linha de código:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Isso cria uma instância do cliente de Serviços Móveis.

7. Substitua os valores de **APPURL** e **APPKEY** neste código pela URL e a chave do aplicativo do serviço móvel adquirido na etapa 6.

8. Localize a seguinte linha de código comentada:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Após esse comentário, adicione a seguinte linha de código:

        self.table = [self.client tableWithName:@"TodoItem"];

    Isso cria a instância da tabela TodoItem.

9. Localize a seguinte linha de código comentada:

 	    // Create a predicate that finds items where complete is false

    Após esse comentário, adicione a seguinte linha de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Isso cria uma consulta para retornar todas as tarefas que ainda não foram concluídas.

10. Localize a seguinte linha de código comentada:

        // Query the TodoItem table and update the items property with the results from the service

   	Substitua o comentário e a invocação de bloco **completion** subsequente pelo seguinte código:

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Localize o método **addItem** e substitua o corpo do método pelo seguinte código:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Esse código envia uma solicitação de inserção ao serviço móvel.

12. Localize o método **completeItem** e localize a seguinte linha de código comentada:

        // Update the item in the TodoItem table and remove from the items array on completion

    Substitua o corpo do método, daqui para o final do método, pelo código a seguir:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Este código remove TodoItems depois que eles estiverem marcados como concluídos.

13. Em TodoListController.m, localize o método **onAdd** e substitua-o pelo código a seguir:

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


Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.
