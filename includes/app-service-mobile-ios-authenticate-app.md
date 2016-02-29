**Objective-C**:

1. Em seu Mac, abra _QSTodoListViewController.m_ em Xcode e adicione o método a seguir. Altere _google_ para _microsoftaccount_, _twitter_, _facebook_ ou _windowsazureactivedirectory_ se não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, [você precisará colocar os domínios do Facebook na lista branca em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Substitua `[self refresh]` em `viewDidLoad` em _QSTodoListViewController.m_ pelo seguinte:

            [self loginAndGetData];

3. Pressione _Executar_ para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

**Swift**:

1. Em seu Mac, abra _ToDoTableViewController.swift_ em Xcode e adicione o método a seguir. Altere _google_ para _microsoftaccount_, _twitter_, _facebook_ ou _windowsazureactivedirectory_ se não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, [você precisará colocar os domínios do Facebook na lista branca em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` ao final de `viewDidLoad()` em _ToDoTableViewController.swift_. Adicione uma chamada para `loginAndGetData()` em seu lugar:

            loginAndGetData()

3. Pressione _Executar_ para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!---HONumber=AcomDC_0218_2016-->