<properties 
	pageTitle="Crie aplicativos em tempo real com o Pusher (iOS) - Serviços Móveis" 
	description="Aprenda a usar o Pusher para enviar notificações para seu aplicativo do Serviços de Mídia do Azure no iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/05/2015" 
	ms.author="donnam"/>


# Crie aplicativos em tempo real com os Serviços Móveis e o Pusher
<div class="dev-center-tutorial-selector sublanding">
	<a href="" title="iOS" class="current">iOS</a>
</div>

Este tópico mostra como adicionar funcionalidade em tempo real a seu aplicativo baseado nos Serviços Móveis do Azure. Ao concluir, os dados da TodoList estarão sincronizados, em tempo real, em todas as instâncias em execução de seu aplicativo.

O tutorial [Enviar notificações por push a usuários][] mostra como usar notificações por push para informar os usuários sobre novos itens na lista de tarefas pendentes. As notificações por push são uma excelente maneira de mostrar alterações ocasionais. No entanto, às vezes, um aplicativo precisa de notificações frequentes em tempo real. As notificações em tempo real podem ser adicionadas a seu serviço móvel usando a API Pusher. Neste tutorial, usamos o Pusher com os Serviços Móveis para manter uma lista de Tarefas Pendentes sincronizadas quando são feitas alterações em qualquer instância em execução do aplicativo.

O Pusher é um serviço baseado em nuvem que, como os Serviços Móveis, facilita incrivelmente a criação de aplicativos em tempo real. Você pode usar o Pusher para criar rapidamente pesquisas ao vivo, salas de chat, jogos com vários participantes, aplicativos de colaboração, para transmissão de dados e conteúdo ao vivo e isso é só o começo! Para saber mais, confira [http://pusher.com](http://pusher.com).

Este tutorial explica as etapas básicas para adicionar colaboração em tempo real ao aplicativo de lista de tarefas pendentes:

1. [Criar uma conta no Pusher][]
2. [Atualizar seu aplicativo][]
3. [Instalar scripts de servidor][]
4. [Testar seu aplicativo][]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][].

## <a name="sign-up"></a>Criar uma nova conta no Pusher

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Atualizar seu aplicativo

Agora que você possui uma conta do Pusher configurada, a próxima etapa é modificar o código do aplicativo iOS para as novas funcionalidades.

###Instalar a biblioteca libPusher

A biblioteca [libPusher][] permite acessar o Pusher no iOS.

1. Baixe a biblioteca libPusher [aqui][libPusherDownload].

2. Crie um grupo chamado _libPusher_ em seu projeto.

3. No Localizador, descompacte o arquivo zip baixado, selecione as pastas **libPusher-combined.a** e **/headers** e arraste esses itens para o grupo **libPusher** de seu projeto.

4. Selecione **Copiar itens na pasta do grupo de destino** e, em seguida, clique em **Concluir**.

	![][add-files-to-group]

   Isso copiará os arquivos do libPusher em seu projeto.

5. Na raiz do projeto no Gerenciador de Projetos, clique em **Fases de Criação** e, em seguida, clique em **Adicionar Fase de Criação** e **Adicionar Fase de Criação**.

6. Arraste o arquivo **libPusher-combined.a** do Gerenciador de Projetos para a nova fase de criação.

7. Alterar o **Destino** para **Estruturas** e clique em **Copiar somente ao instalar**.

	![][add-build-phase]

8. Na área **Link binário com bibliotecas**, adicione as seguintes bibliotecas:

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. Finalmente, em **Criar Configurações**, localize a configuração da compilação de destino definindo **Outros Sinalizadores do Vinculador** e adicione o sinalizador **-all_load**.

	![][add-linker-flag]

	Isto mostra o sinalizador **-all_load** definido para o destino de Depurar compilação.

A biblioteca agora está instalada e pronta para uso.

### Adicionar código ao aplicativo

1. No Xcode, abra o arquivo **QSTodoService.h** e adicione as seguintes declarações de método:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. Substitua as declarações existentes de **addItem** e **completeItem** pelo seguinte:

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. Em **QSTodoService.m**, adicione o seguinte código para implementar os novos métodos:

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	O QSTodoService agora permite que você localize itens pela **id** e adicione e preencha itens localmente sem enviar solicitações explícitas ao serviço remoto.

4. Substitua os métodos **addItem** e **completeItem** existentes pelo seguinte código:

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	Observe que agora os itens são adicionados e preenchidos, junto com as atualizações na interface do usuário, quando os eventos são recebidos do Pusher em vez de quando a tabela de dados é atualizada.

5. No arquivo **QSTodoListViewController.h**, adicione as seguintes instruções de importação:

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. Modifique a declaração da interface para adicionar **PTPusherDelegate** para que seja semelhante ao seguinte:

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. Adicione a seguinte nova propriedade:

		@property (nonatomic, strong) PTPusher *pusher;

8. Adicione o seguinte código que declara um novo método:

		// Sets up the Pusher client
		- (void) setupPusher;

9. Em **QSTodoListViewController.m**, adicione a seguinte linha sob as outras linhas de **@synthesise** para implementar a nova propriedade:

		@synthesize pusher = _pusher;

10. Agora, adicione o seguinte código para implementar o novo método:

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. Substitua o espaço reservado `**your_app_key**` pelo valor app_key copiado na caixa de diálogo Informações de Conexão anteriormente.

12. Substitua o método **onAdd** pelo seguinte código:

		- (IBAction)onAdd:(id)sender
		{
		    if (itemText.text.length  == 0) {
        		return;
		    }

		    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
		    [self.todoService addItem:item];

		    itemText.text = @"";
		}

13. No arquivo **QSTodoListViewController.m**, localize o método (void)viewDidLoad e adicione uma chamada para o método **setupPusher** para que as primeiras linhas sejam:

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    [self setupPusher];

14. No final do método **tableView:commitEditingStyle:forRowAtIndexPath**, substitua a chamada para **completeItem** pelo seguinte código:

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

O aplicativo agora pode receber eventos do Pusher e atualizar a lista itens ToDo adequadamente.



##<a name="install-scripts"></a>Instalar scripts de servidor



Só falta configurar seus scripts de servidor. Podemos inserir um script para quando um item for inserido ou atualizado na tabela TodoList.



1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.


2. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

	![][1]



3. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.


	![][2]



	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.


4. Substitua a função de inserção pelo seguinte código:


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. Substitua os espaços reservados no script acima pelos valores copiados na caixa de diálogo Informações de Conexão anteriormente:

	- **`**your_app_id**`**: o app&#95;valor da id
	- **`**your_app_key**`**: o app&#95;valor da chave
	- **`**your_app_key_secret**`**: o app&#95; chave&#95;segredo


6. Clique no botão **Salvar**. Você configurou um script para publicar um evento no Pusher sempre que um novo item for inserido na tabela **TodoItem**.


7. Selecione **Atualizar** no menu suspenso **Operação**.


8. Substitua a função de atualização pelo seguinte código:

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. Repita a Etapa 5 para esse script para substituir os espaços reservados.


10. Clique no botão **Salvar**. Você configurou um script para publicar um evento no Pusher sempre que um novo item for atualizado.



##<a name="test-app"></a>Testar seu aplicativo



Para testar o aplicativo, você precisará executar duas instâncias. Você pode executar uma instância em um dispositivo iOS e outra no simulador de iOS.

1. Conecte o dispositivo iOS, pressione o botão **Executar** (ou a tecla Command+R) para iniciar o aplicativo no dispositivo e, em seguida, interrompa a depuração.

	Agora você tem o aplicativo instalado no dispositivo.

2. Execute o aplicativo no simulador do iOS e, ao mesmo tempo, inicie o aplicativo no dispositivo iOS.

	Agora, você tem duas instâncias do aplicativo em execução.

3. Adicione um novo item de tarefa pendente em uma das instâncias do aplicativo.

	Verifique se o item adicionado aparece na outra instância.

4. Selecione um item de tarefa pendente para marcá-lo como concluído em uma instância do aplicativo.

	Verifique se o item desaparece da outra instância.

Parabéns, você configurou com êxito o aplicativo de seu serviço móvel para sincronização em tempo real em todos os clientes.

## <a name="nextsteps"> </a>Próximas etapas

Agora que você já viu como é fácil usar o serviço Pusher com os Serviços Móveis, siga estes links para obter mais informações sobre o Pusher.

-   Documentação da API Pusher: <http://pusher.com/docs>
-   Tutoriais do Pusher: <http://pusher.com/tutorials>

Para obter mais informações sobre como registrar e usar scripts de servidor, consulte [Referência de script do servidor de Serviços Móveis].

<!-- Anchors. -->
[Criar uma conta no Pusher]: #sign-up
[Atualizar seu aplicativo]: #update-app
[Instalar scripts de servidor]: #install-scripts
[Testar seu aplicativo]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[Enviar notificações por push a usuários]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

[Referência de script do servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
 

<!---HONumber=July15_HO1-->