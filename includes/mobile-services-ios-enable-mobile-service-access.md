Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  Se você ainda não tiver instalado o [SDK do iOS dos Serviços Móveis][], instale-o agora.

2.  No Navegador de Projetos no Xcode, abra os arquivos TodoService.m e TodoService.h localizados na pasta Quickstart e adicione a seguinte instrução de importação:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  No arquivo ToDoService.h, localize a seguinte linha de código comentada:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    Após esse comentário, adicione a seguinte linha de código:

        @property (nonatomic, strong)   MSClient *client;

    Isso cria uma propriedade que representa o MSClient que se conecta ao serviço

4.  No arquivo TodoService.m, localize a seguinte linha de código comentada:

        // Create an MSTable property for your items. 

    Após esse comentário, adicione a seguinte linha de código dentro da declaração @interface:

        @property (nonatomic, strong)   MSTable *table;

    Isso cria uma representação de propriedade para a tabela de serviços móveis.

5.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

6.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

    Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

7.  De volta no Xcode, abra TodoService.m e localize a seguinte linha de código comentada:

        // Initialize the Mobile Service client with your URL and key.

    Após esse comentário, adicione a seguinte linha de código:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Isso cria uma instância do cliente de Serviços Móveis.

8.  Substitua os valores de **APPURL** e **APPKEY** neste código pela URL e a chave do aplicativo do serviço móvel adquirido na etapa 6.

9.  Localize a seguinte linha de código comentada:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Após esse comentário, adicione a seguinte linha de código:

        self.table = [self.client getTable:@"TodoItem"];

    Isso cria a instância da tabela TodoItem.

10. Localize a seguinte linha de código comentada:

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Após esse comentário, adicione a seguinte linha de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Isso cria uma consulta para retornar todas as tarefas que ainda não foram concluídas.

11. Localize a seguinte linha de código comentada:

        // Query the TodoItem table and update the items property with the results from the service.

    Substitua o comentário e a invocação de bloco **completion** subsequente pelo seguinte código:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Localize o método **addItem** e adicione o seguinte código ao corpo do método:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Esse código envia uma solicitação de inserção ao serviço móvel.

13. Localize o método **completeItem** e adicione o seguinte código ao corpo do método:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Este código remove TodoItems depois que eles estiverem marcados como concluídos.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

  
