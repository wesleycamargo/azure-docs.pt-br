<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Tratar conflitos com dados offline nos Servi&ccedil;os M&oacute;veis (iOS) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Servi&ccedil;os M&oacute;veis do Azure para tratar conflitos ao sincronizar dados offline no seu aplicativo iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Tratamento de conflitos de dados offline nos Servi&ccedil;os M&oacute;veis" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure. Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados offline][Introdução aos dados offline]. Antes de começar este tutorial, você deve primeiro concluir a [Introdução aos dados offline][Introdução aos dados offline].

> [WACOM.NOTE] Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

## Concluir a Introdução com o tutorial offline

Siga as instruções no tutorial [Introdução aos dados offline][Introdução aos dados offline] e conclua esse projeto. Usaremos o projeto concluído por meio daquele tutorial como ponto de partida para este tutorial.

## Atualizar o projeto de aplicativo para permitir edição

Vamos atualizar o projeto concluído por meio de [Introdução aos dados offline][Introdução aos dados offline] para permitir a edição dos itens. No momento, se você executar esse mesmo aplicativo em dois telefones, trocar o mesmo item em ambos os telefones localmente e enviar as alterações por push de volta ao servidor, a operação falhará com um conflito.

Os recursos de sincronização offline no SDK permitem tratar de tais conflitos via código e decidir dinamicamente o que fazer com itens conflitantes. Alterar o projeto de início rápido permite-nos experimentar com esse recurso.

### Atualizar o Controlador de Visualização de Lista ToDo

1.  Vamos atualizar o storyboard do iPhone. Se estiver trabalhando com um iPad, siga as mesmas etapas para o storyboard do iPad também.

2.  Selecione **MainStoryboard\_iPhone.storyboard** no Navegador de Projeto Xcode e selecione **Controlador de Visualização de Lista ToDo**. No menu superior, clique em **Editor -\> Inserir em -\> Controlador de Navegação**

    ![][0]

3.  A seguir, no **Controlador de Visualização de Lista ToDo**, selecione a célula de visualização de tabela e defina o modo de Acessório para **Divulgar indicador**. O indicador de divulgação mostra aos usuários que se eles tocarem no controlador de visualização de tabela associado, uma nova visualização será exibida. O indicador de divulgação não produz eventos.

    ![][1]

4.  Em **TodoListViewController.m**, remova as seguintes operações e seus conteúdos por completo. Não precisamos deles:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Adicionar controlador de visualização de item ToDo

1.  Adicione uma nova classe Objective-C chamada **QSTodoItemViewController**, derivada de **UIViewController**, ao seu projeto:

    ![][2]

    ![][3]

2.  Em **QSTodoItemViewController.h**, adicione uma propriedade para conter o item a ser modificado:

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  Em **QSTodoItemViewController.m**, adicione duas propriedades privadas para os dois campos do item ToDo que editaremos -- o status de conclusão e o texto do item ToDo em si:

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  Em **QSTodoItemViewController.m**, atualize a subimplantação de **viewDidLoad** ao seguinte código:

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

5.  Em **QSTodoItemViewController.m**, adicione mais quatro métodos para tratar vários eventos:

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

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Adicionar controlador de visualização de item ToDo e Segue ao storyboard

1.  Volte para o arquivo **MainStoryboard\_iPhone.storyboard** usando o Navegador de Projeto.

2.  Adicione um novo controlador de visualização para o Item ToDo ao storyboard, à direita do **Controlador de Visualização ToDo** existente. Defina a classe personalizada desse novo controlador de visualização para **QSTodoItemViewController**. Para obter mais informações, consulte [Adicionando uma cena a um Storyboard][Adicionando uma cena a um Storyboard].

3.  Adicione um segue por push usando o **Controlador de Visualização de Lista ToDo** ao **Controlador de Visualização de Item ToDo** e dê ao segue o nome **detailSegue**. Para obter mais informações, consulte [Adicionando um segue entre cenas em um storyboard][Adicionando um segue entre cenas em um storyboard]. Não crie esse segue por meio de nenhuma célula ou botão no controlador de visualização de origem. Em vez disso, use CTRL + Arrastar do ícone do controlador de visualização abaixo do **Controlador de Visualização de Lista ToDo** na interface do storyboard para o **Controlador de Visualização de Item ToDo**. Se você acidentalmente aplicar segue de uma célula acionará o segue duas vez quando executar o aplicativo, resultando neste erro:

        Nested push animation can result in corrupted navigation bar

4.  Adicione um campo de texto para o texto do item e um controle segmentado para o status de conclusão ao novo **Controlador de Visualização de Item ToDo**, com os rótulos também. No controle segmentado, defina o título de **Segmento 0** para **Sim** e o título de **Segmento 1** para **Não**. Conecte esses novos campos para saídas no código. Para saber mais, consulte [Criar uma Interface do Usuário][Criar uma Interface do Usuário] e [Controles Segmentados][Controles Segmentados].

    ![][4]

5.  Conecte esses novos campos às saídas correspondentes já adicionadas ao **QSTodoItemViewController.m**. Conecte o campo de texto do item à saída **itemText** e o controle segmentado de status de conclusão à saída **itemComplete**. Para obter mais informações, consulte [Criando uma conexão de saída][Criando uma conexão de saída].

6.  Defina o delegado do campo de texto para o controlador de visualização. Isso reprojeta o campo de texto quando você porventura editar um item e pressionar RETORNAR. Use CTRL + Arrastar do campo de texto para o ícone do controlador de visualização abaixo do **Controlador de Visualização de Item ToDo** na interface do storyboard e selecione a saída do delegado; isso indica para o storyboard que o delegado desse campo de texto está no controlador de visualização.

7.  Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Você verá o controlador de visualização do item (atualmente vazio).

    ![][5]

    ![][6]

### Adicionar detalhes do item ao Controlador de Visualização de Item ToDo

1.  Faremos referência ao **QSTodoItemViewController** por meio do **QSTodoListViewController.m**. Assim, no **QSTodoListViewController.m**, vamos adicionar uma linha para importar **QSTodoItemViewController.h**.

        #import "QSTodoItemViewController.h"

2.  Adicionar duas novas propriedades à interface **QSTodoListViewController** no **QSTodoListViewController.m** para armazenar o item sendo editado:

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  Implante **tableView:didSelectRowAtIndexPath:** no **QSTodoListViewController.m** para salvar o item sendo editado e então chamar o segue para exibir a visualização detalhada.

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  Implante **prepareForSegue:sender:** no **QSTodoListViewController.m** para passar o item para o **Controlador de Visualização de Item ToDo**.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Você verá que o controlador de visualização de item não está mais vazio -- ele exibe os detalhes dos itens ToDo.

    ![][7]

### Adicionar suporte para salvar edições

1.  Ao clicar no botão “Voltar” na visualização de navegação, as edições são perdidas. Enviamos dados para a visualização detalhada, mas os dados não foram enviados de volta à visualização mestre. Uma vez que já passamos um ponteiro para uma cópia do item, podemos usar esse ponteiro para recuperar a lista de atualizações feitas ao item e atualizá-lo no servidor. Para começar, primeiro atualize a classe de wrapper do servidor de **QSTodoService** em **QSTodoService.m** removendo a operação **completeItem** e adicionando uma nova operação **updateItem**. Isso acontece porque **completeItem** somente marca itens como concluídos; em vez disso, **updateItem** atualiza os itens.

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  Remova a declaração para **completeItem** de **QSTodoService.h** e adicione essa declaração a **updateItem**:

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  Em **QSTodoListViewController.m**, adicione a operação **viewWillAppear** para chamar o método de atualização sempre que a visualização mestre for exibida depois de retornar do controlador da visualização de detalhes.

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  Agora vamos testar o aplicativo. Verifique se o aplicativo funciona com todas as mudanças feitas até o momento. Execute o aplicativo agora no simulador. Adicione itens à lista ToDo e então clique neles. Tente editar um item e volte. Verifique se a descrição do item é atualizada na visualização mestre do aplicativo. Atualize o aplicativo usando o gesto de arrastar para baixo e verifique se as edições se refletem na nuvem.

### Problema de tratamento de conflito

1.  Vamos examinar o que acontece quando dois clientes diferentes tentam modificar o mesmo dado ao mesmo tempo. Na lista de exemplos abaixo, há um item "Hello world 3" Vamos alterá-lo para que diga "Hello world 13" no dispositivo e alterar isso para "Hello world 23" em outro dispositivo.

    ![][8]

2.  Abra o aplicativo em dois locais: em dois dispositivos iOS ou no simulador e em um dispositivo iOS. Se você não tiver um dispositivo físico no qual testar, inicie uma instância no simulador e, usando um cliente REST, envie uma solicitação de PATCH para o serviço móvel. O URL da solicitação de PATCH reflete o nome do Serviço Móvel, o nome da tabela do item ToDo e a ID da tabela do item ToDo que você está editando, enquanto o cabeçalho do aplicativo x-zumo é a chave de aplicativo:

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  Agora atualize os itens nas duas instâncias do aplicativo. Você verá um erro impresso no log de saída no Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

Isso ocorre porque, no bloco de conclusão, na chamada para **pullWithQuery:completion:**, o parâmetro de erro será não nil, o que fará o erro ser impresso na saída usando **NSLog**.

### Atualize QSTodoService para dar suporte à manipulação de conflitos

1.  Vamos permitir ao usuário decidir como manipular o conflito lidando com ele no cliente. Para isso, vamos implantar o protocolo **MSSyncContextDelegate**. Em **QSTodoService.h** e **QSTodoService.m**, altere a declaração do método de fábrica \*\*(QSTodoService \*)defaultService;\*\* para a declaração abaixo de modo a receber o delegado de contexto de sincronização como um parâmetro:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  Em **QSTodoService.m**, altere a linha **init** como mostrado a seguir, recebendo novamente o delegado de contexto de sincronização como um parâmetro:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  Em **QSTodoService.m**, altere a chamada **init** em **defaultServiceWithDelegate** para **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  De volta ao **QSTodoService.m**, altere a inicialização de **self.client.syncContext** para passar **syncDelegate**, em vez de **nil** para o delegado:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### Adicione o Auxiliar de Visualização de Alerta de UI para suporte a manipulação de conflito

1.  Se houver conflito, vamos permitir ao usuário escolher que versão manter:

-   manter a versão do cliente (que substitui a versão no servidor),
-   manter a versão do servidor (que atualiza a tabela local do cliente) ou
-   não manter nenhuma das versões (cancela push e deixe a operação pendente).

Uma vez que outra atualização também pode ocorrer exibimos o prompt, continuaremos mostrando as opções até o servidor parar de retornar uma resposta de falha. No nosso código, vamos usar uma classe auxiliar que mostra uma visualização de alerta e pega um delegado chamado quando a visualização de alerta é exibida. Vamos definir a classe auxiliar **QSUIAlertViewWithBlock** primeiro.

1.  Adicione essa nova classe, **QSUIAlertViewWithBlock**, usando Xcode e substitua **QSUIAlertViewWithBlock.h** pelos seguintes conteúdos:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  A seguir, substitua **QSUIAlertViewWithBlock.m** pelo seguinte arquivo:

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

### Adicione o Manipulador de Conflito ao Controlador de Visualização de Lista ToDo

1.  Em **QSTodoListViewController.m**, substitua a chamada para **defaultService** em **viewDidLoad** por uma chamada para **defaultServiceWithDelegate**, como mostrado a seguir:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  Em **QSTodoListViewController.h**, adicione **\<MSSyncContextDelegate\>** à declaração de interface para que o protocolo **MSSyncContextDelegate** seja implantado.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  Adicione a seguinte declaração de importação na parte superior em **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4.  Por fim, vamos adicionar as duas operações a seguir para usar essa classe auxiliar **QSTodoListViewController.m** e avisar o usuário para reconciliar o conflito de uma de três maneiras.

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

### Testar o aplicativo

Vamos testar o aplicativo com conflitos! Edite o mesmo item em duas instâncias diferentes do aplicativo executando ao mesmo tempo. Agora realize o gesto de atualizar nas instâncias do aplicativo arrastando por meio do topo. Você verá um prompt para reconciliar a alteração agora.

### Resumo

Para configurar o projeto concluído por meio de [Introdução aos dados offline][Introdução aos dados offline] para detectar conflitos, você primeiro adiciona a habilidade de editar e atualizar os itens ToDo.

Para isso, você adicionou um novo controlador de visualização de detalhes do item, conectou o controlador da visualização principal e o controlador da visualização de detalhes e, por fim, adicionou a habilidade de saltar as edições e enviá-las à nuvem por push.

A seguir, descobriu o que acontece quando há um conflito. Você adicionou suporte para um manipulador de conflito implantando o protocolo **MSSyncContextDelegate**. Também habilitou suporte para o delegado de sincronizar contexto por meio da classe da interface do servidor de **QSTodoService**, **QSTodoListViewController** e classes de suporte.

Ao longo do caminho, adicionou uma classe auxiliar **QSUIAlertViewWithBlock** para exibir um alerta aos usuários e terminou adicionando código a **QSTodoListViewController** para avisar o usuário para reconciliar o conflito de uma das três maneiras a seguir.



  [Introdução aos dados offline]: /pt-br/documentation/articles/mobile-services-ios-get-started-offline-data/
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [Adicionando uma cena a um Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [Adicionando um segue entre cenas em um storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [Criar uma Interface do Usuário]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [Controles Segmentados]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [Criando uma conexão de saída]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
