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
	ms.date="09/16/2015"
	ms.author="krisragh;donnam"/>


# Tratamento de conflitos com sincronização de dados offline nos Serviços Móveis

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

Este tópico mostra como sincronizar dados e tratar conflitos ao usar os recursos offline dos Serviços Móveis do Azure. Este tutorial baseia-se no tutorial [Introdução aos dados offline].

>[AZURE.NOTE]Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação Gratuita do Azure</a>.


## Baixar o Projeto do iOS

Para este tutorial, baixe [um projeto Xcode atualizado do Github](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS). Usamos o projeto Xcode do final do tutorial [Guia de introdução aos dados Offline] como um ponto de partida e, em seguida, nós o atualizamos para permitir a edição de itens. Também adicionamos classes e métodos de suporte, para que possamos adicionar um manipulador de conflito na próxima seção.

No final deste tutorial, se você executar esse aplicativo em dois telefones, altere o mesmo item em ambos os telefones localmente e por envie as alterações por push de volta ao servidor; você permitirá que o usuário em cada telefone para escolher qual versão deseja manter: * manter a versão do cliente (que substitui a versão no servidor), * manter a versão do servidor (que atualiza a tabela de cliente local) ou * não manter nenhuma versão (cancela o envio por push e deixa a operação pendente).

Agora, vamos adicionar o manipulador de conflito para habilitar essa funcionalidade.

## <a name="add-conflict-handling"></a>Adicione o Manipulador de Conflito ao Controlador de Visualização de Lista ToDo

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

## <a name="test-app"></a>Testar o aplicativo

Vamos testar o aplicativo com conflitos! Edite o mesmo item em duas instâncias diferentes do aplicativo em execução simultânea, ou usando o aplicativo e um cliente REST.

Agora realize o gesto de atualização das instâncias do aplicativo, arrastando por meio do topo. Agora você verá um prompt para reconciliar o conflito:

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Guia de introdução aos dados Offline]: mobile-services-ios-get-started-offline-data.md
[Introdução aos dados offline]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->