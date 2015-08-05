<properties
	pageTitle="Tratar conflitos de dados offline nos serviços móveis (iOS) | Centro de desenvolvimento móvel"
	description="Saiba como usar os Serviços Móveis do Azure para tratar conflitos ao sincronizar dados offline no seu aplicativo iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015" 
	ms.author="krisragh;donnam"/>


# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure. Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados offline].

>[AZURE.NOTE]Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação Gratuita do Azure</a>.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o projeto de aplicativo para permitir edição]
2. [Atualizar o controlador de visualização da lista de tarefas pendentes]
3. [Adicionar controlador de visualização de item ToDo]
4. [Adicionar controlador de visualização de item pendente e Segue ao storyboard]
5. [Adicionar detalhes do item ao controlador de visualização de Item ToDo]
6. [Adicionar suporte para salvar edições]
7. [Problema de tratamento de conflito]
8. [Atualize QSTodoService para dar suporte ao tratamento de conflitos]
9. [Adicione o Auxiliar de Visualização de Alerta de UI para dar suporte ao tratamento de conflito]
10. [Adicione o Manipulador de Conflito ao Controlador de Visualização de Lista ToDo]
11. [Testar o aplicativo]

## Concluir a Introdução com o tutorial offline

Siga as instruções no tutorial [Introdução aos dados offline] e conclua esse projeto. Usaremos o projeto concluído por meio daquele tutorial como ponto de partida para este tutorial.

## <a name="update-app"></a>Atualizar o projeto de aplicativo para permitir edição

Vamos atualizar o projeto concluído por meio de [Introdução aos dados offline] para permitir a edição dos itens. No momento, se você executar esse mesmo aplicativo em dois telefones, trocar o mesmo item em ambos os telefones localmente e enviar as alterações por push de volta ao servidor, a operação falhará com um conflito.

Os recursos de sincronização offline no SDK permitem tratar de tais conflitos via código e decidir dinamicamente o que fazer com itens conflitantes. Alterar o projeto de início rápido permite-nos experimentar com esse recurso.

### <a name="update-list-view"></a>Atualizar o Controlador de Visualização de Lista ToDo

1. Selecione **MainStoryboard_iPhone.storyboard** no Navegador de Projeto Xcode e selecione **Controlador de Visualização de Lista ToDo**. Selecione a célula de visualização de tabela e defina seu modo Acessório como **Indicador de Divulgação**. O indicador de divulgação mostra aos usuários que se eles tocarem no controlador de visualização de tabela associado, uma nova visualização será exibida. O indicador de divulgação não produz eventos.

      ![][update-todo-list-view-controller-2]

2. Em **TodoListViewController.m**, remova as seguintes operações e seus conteúdos por completo. Não precisamos deles:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Adicionar controlador de visualização de item ToDo

1. Crie uma nova classe Cocoa Touch denominada **QSItemViewController**, derivada de **UIViewController**.

2. Em **QSItemViewController.h**, adicione a seguinte definição de tipo:

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. Em **QSItemViewController.h**, adicione uma propriedade para armazenar o item a ser modificado e uma propriedade para o retorno de chamada que é invocado depois que o usuário pressiona o botão Voltar na exibição de Detalhes:

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. Em **QSItemViewController.m**, adicione duas propriedades privadas para os dois campos do item ToDo que editaremos -- o status de conclusão e o texto do item ToDo em si:

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. Em **QSItemViewController.m**, atualize a subimplantação de **viewDidLoad** para o seguinte código:

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

6. Em **QSItemViewController.m**, adicione mais quatro métodos para tratar eventos de controle editados:

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

7. Em **QSItemViewController**, adicione também o seguinte método, que é chamado quando o usuário pressiona o botão **Voltar** na barra de navegação. O método pode ser chamado em outros eventos, portanto, verificamos primeiro o modo de exibição pai. Se houve qualquer mudança no item, **self.item** é modificado e o retorno de chamada **editCompleteBlock** é chamado:

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];

                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;

                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];

                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Adicionar controlador de visualização de item ToDo e Segue ao storyboard

1. Volte para o arquivo **MainStoryboard_iPhone.storyboard** usando o Navegador de Projeto.

2. Adicione um novo controlador de visualização para o Item ToDo ao storyboard, à direita do **Controlador de Visualização ToDo** existente. Definir a classe personalizada desse novo controlador de exibição como **QSItemViewController**. Para obter mais informações, consulte [Adicionando uma cena a um Storyboard].

3. Adicionar um segue **Mostrar** do **Controlador de exibição de lista ToDo** ao **Controlador de exibição de itens ToDo**. Em seguida, no Inspetor de atributos, defina o identificador de segue **detailSegue**.

    Não crie esse segue por meio de nenhuma célula ou botão no controlador de visualização de origem. Em vez disso, pressione CTRL + arraste a partir do ícone de controlador de exibição acima do **controlador de exibição de lista ToDo** na interface do storyboard para o **Controlador de exibição de itens ToDo**:

    ![][todo-list-view-controller-add-segue]

    Se você acidentalmente aplicar segue de uma célula acionará o segue duas vez quando executar o aplicativo, resultando neste erro:

        Nested push animation can result in corrupted navigation bar

    Para saber mais sobre segues, confira [Adicionando um Segue entre cenas em um Storyboard].

4. Adicione um campo de texto para o texto do item e um controle segmentado para o status de conclusão ao novo **Controlador de Visualização de Item ToDo**, com os rótulos também. No controle segmentado, defina o título de **Segmento 0** para **Sim** e o título de **Segmento 1** para **Não**. Conecte esses novos campos para saídas no código. Para saber mais, consulte [Criar uma Interface do Usuário] e [Controles Segmentados].

      ![][add-todo-item-view-controller-3]

5. Conecte esses novos campos às saídas correspondentes que você já adicionou a **QSItemViewController.m**. Conecte o campo de texto do item à saída **itemText** e o controle segmentado de status de conclusão à saída **itemComplete**. Para obter mais informações, consulte [Criando uma conexão de saída].

6. Defina o delegado do campo de texto para o controlador de visualização. Use CTRL + Arrastar do campo de texto para o ícone do controlador de visualização abaixo do **Controlador de Visualização de Item ToDo** na interface do storyboard e selecione a saída do delegado; isso indica para o storyboard que o delegado desse campo de texto está no controlador de visualização.

7. Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Você verá o controlador de visualização do item (atualmente vazio).

      ![][add-todo-item-view-controller-4]
      ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>Adicionar detalhes do item ao Controlador de Visualização de Item ToDo

1. Faremos referência ao **QSItemViewController** por meio do **QSTodoListViewController.m**. Assim, no **QSTodoListViewController.m**, vamos adicionar uma linha para importar **QSItemViewController.h**.

        #import "QSItemViewController.h"

2. Adicione a nova propriedade à interface **QSTodoListViewController** em **QSTodoListViewController.m** para armazenar o item que está sendo editado:

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. Implante **tableView:didSelectRowAtIndexPath:** no **QSTodoListViewController.m** para salvar o item sendo editado e então chamar o segue para exibir a visualização detalhada.

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary

            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. Implemente **prepareForSegue:sender:** em **QSTodoListViewController.m** para passar o item para o **Controlador de exibição de itens ToDo**, e especifique o retorno de chamada quando o usuário sai do modo de exibição de detalhes:

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];

                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Você verá que o controlador de visualização de item não está mais vazio -- ele exibe os detalhes dos itens ToDo.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>Adicionar suporte para salvar edições

1. Ao clicar no botão “Voltar” na visualização de navegação, as edições são perdidas. Enviamos dados para a visualização detalhada, mas os dados não foram enviados de volta à visualização mestre. Uma vez que já passamos um ponteiro para uma cópia do item, podemos usar esse ponteiro para recuperar a lista de atualizações feitas ao item e atualizá-lo no servidor. Para começar, primeiro atualize a classe de wrapper do servidor de **QSTodoService** em **QSTodoService.m** removendo a operação **completeItem** e adicionando uma nova operação **updateItem**. Isso acontece porque **completeItem** somente marca itens como concluídos; em vez disso, **updateItem** atualiza os itens.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. Remova a declaração para **completeItem** de **QSTodoService.h** e adicione essa declaração a **updateItem**:

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. Agora vamos testar o aplicativo. Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Tente editar um item e volte. Verifique se a descrição do item é atualizada na visualização mestre do aplicativo. Atualize o aplicativo usando o gesto de arrastar para baixo e verifique se as edições se refletem em seu serviço remoto.

### <a name="conflict-handling-problem"></a>Problema de tratamento de conflito

1. Vamos examinar o que acontece quando dois clientes diferentes tentam modificar o mesmo dado ao mesmo tempo. Na lista de exemplo a seguir, há um item "Serviços móveis é legal!" Vamos alterar isso para, digamos, "Eu adoro serviços móveis!" em um dispositivo e vamos alterar isso para "Adoro Azure!" em outro dispositivo.

      ![][conflict-handling-problem-1]

2. Iniciar o aplicativo em dois lugares: em dois dispositivos iOS, ou no simulador e em um dispositivo iOS. Se você não tiver um dispositivo físico no qual testar, inicie uma instância no simulador e, usando um cliente REST, envie uma solicitação de PATCH para o serviço móvel. O URL da solicitação de PATCH reflete o nome do Serviço Móvel, o nome da tabela do item ToDo e a ID da tabela do item ToDo que você está editando, enquanto o cabeçalho do aplicativo x-zumo é a chave de aplicativo:

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. Agora atualize os itens nas duas instâncias do aplicativo. Você verá um erro impresso no log de saída no Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  Isso ocorre porque, no bloco de conclusão, na chamada para **pullWithQuery:completion:**, o parâmetro de erro será não nil, o que fará o erro ser impresso na saída usando **NSLog**.

### <a name="service-add-conflict-handling"></a>Atualize QSTodoService para dar suporte à manipulação de conflitos

1. Vamos permitir ao usuário decidir como manipular o conflito lidando com ele no cliente. Para isso, vamos implantar o protocolo **MSSyncContextDelegate**. Em **QSTodoService.h** e em **QSTodoService.m**, altere a declaração do método de fábrica **(QSTodoService *)defaultService;** para a declaração abaixo de modo a receber o delegado de contexto de sincronização como um parâmetro:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. Em **QSTodoService.m**, altere a linha **init** como mostrado a seguir, recebendo novamente o delegado de contexto de sincronização como um parâmetro:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3. Em **QSTodoService.m**, altere a chamada **init** em **defaultServiceWithDelegate** para **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. De volta ao **QSTodoService.m**, altere a inicialização de **self.client.syncContext** para passar **syncDelegate**, em vez de **nil** para o delegado:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>Adicione o Auxiliar de Visualização de Alerta de UI para suporte a manipulação de conflito

1. Se houver conflito, vamos permitir ao usuário escolher que versão manter:
  * manter a versão do cliente (que substitui a versão no servidor),
  * manter a versão do servidor (que atualiza a tabela local do cliente) ou
  * não manter nenhuma das versões (cancela push e deixe a operação pendente).

  Uma vez que outra atualização também pode ocorrer exibimos o prompt, continuaremos mostrando as opções até o servidor parar de retornar uma resposta de falha. No nosso código, vamos usar uma classe auxiliar que mostra uma visualização de alerta e pega um delegado chamado quando a visualização de alerta é exibida. Vamos definir a classe auxiliar **QSUIAlertViewWithBlock** primeiro.

2. Adicione essa nova classe, **QSUIAlertViewWithBlock**, usando Xcode e substitua **QSUIAlertViewWithBlock.h** pelos seguintes conteúdos:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. A seguir, substitua **QSUIAlertViewWithBlock.m** pelo seguinte arquivo:

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### <a name="add-conflict-handling"></a>Adicione o Manipulador de Conflito ao Controlador de Visualização de Lista ToDo

1. Em **QSTodoListViewController.m**, edite **viewDidLoad**. Substitua a chamada para **defaultService**, com uma chamada para **defaultServiceWithDelegate** em vez disso:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. Em **QSTodoListViewController.h**, adicione **&lt;MSSyncContextDelegate&gt;** à declaração de interface para que o protocolo **MSSyncContextDelegate** seja implantado.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Adicione a seguinte declaração de importação na parte superior em **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Por fim, vamos adicionar as duas operações a seguir para usar essa classe auxiliar **QSTodoListViewController.m** e avisar o usuário para reconciliar o conflito de uma de três maneiras.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### <a name="test-app"></a>Testar o aplicativo

Vamos testar o aplicativo com conflitos! Edite o mesmo item em duas instâncias diferentes do aplicativo em execução simultânea, ou usando o aplicativo e um cliente REST.

Agora realize o gesto de atualização das instâncias do aplicativo, arrastando por meio do topo. Agora você verá um prompt para reconciliar o conflito:

![][conflict-ui]


### Resumo

Para configurar o projeto concluído por meio de [Introdução aos dados offline] para detectar conflitos, você primeiro adiciona a habilidade de editar e atualizar os itens ToDo.

Para isso, você adicionou um novo controlador de visualização de detalhes do item, conectou o controlador da visualização principal e o controlador da visualização de detalhes e, por fim, adicionou a habilidade de saltar as edições e enviá-las à nuvem por push.

A seguir, descobriu o que acontece quando há um conflito. Você adicionou suporte para um manipulador de conflito implantando o protocolo **MSSyncContextDelegate**. Também habilitou suporte para o delegado de sincronizar contexto por meio da classe da interface do servidor de **QSTodoService**, **QSTodoListViewController** e classes de suporte.

Ao longo do caminho, adicionou uma classe auxiliar **QSUIAlertViewWithBlock** para exibir um alerta aos usuários e terminou adicionando código a **QSTodoListViewController** para avisar o usuário para reconciliar o conflito de uma das três maneiras a seguir.

<!-- URLs. -->

[Atualizar o projeto de aplicativo para permitir edição]: #update-app
[Atualizar o controlador de visualização da lista de tarefas pendentes]: #update-list-view
[Adicionar controlador de visualização de item ToDo]: #add-view-controller
[Adicionar controlador de visualização de item pendente e Segue ao storyboard]: #add-segue
[Adicionar detalhes do item ao controlador de visualização de Item ToDo]: #add-item-details
[Adicionar suporte para salvar edições]: #saving-edits
[Problema de tratamento de conflito]: #conflict-handling-problem
[Atualize QSTodoService para dar suporte ao tratamento de conflitos]: #service-add-conflict-handling
[Adicione o Auxiliar de Visualização de Alerta de UI para dar suporte ao tratamento de conflito]: #add-alert-view
[Adicione o Manipulador de Conflito ao Controlador de Visualização de Lista ToDo]: #add-conflict-handling
[Testar o aplicativo]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Controles Segmentados]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Criando uma conexão de saída]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Criar uma Interface do Usuário]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adicionando um Segue entre cenas em um Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adicionando uma cena a um Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Introdução aos dados offline]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!----HONumber=July15_HO4-->