**Objective-C**:

1. Em seu Mac, abra *QSTodoListViewController.m* em Xcode e adicione o método a seguir. Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, [você precisará colocar os domínios do Facebook na lista branca em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
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
2. Substitua `[self refresh]` em `viewDidLoad` em *QSTodoListViewController.m* pelo seguinte:
   
            [self loginAndGetData];
3. Pressione *Executar* para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

**Swift**:

1. Em seu Mac, abra *ToDoTableViewController.swift* em Xcode e adicione o método a seguir. Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se usar o Facebook, [você precisará colocar os domínios do Facebook na lista branca em seu aplicativo](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` ao final de `viewDidLoad()` em *ToDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` em seu lugar:
   
            loginAndGetData()
3. Pressione *Executar* para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!---HONumber=AcomDC_0218_2016-->