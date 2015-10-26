1. Em seu Mac, abra **QSTodoListViewController.m** em Xcode e adicione o método a seguir. Altere _facebook_ para _contadamicrosoft_, _twitter_, _google_ ou _activedirectorydowindowsazure_ se não estiver usando o Facebook como seu provedor de identidade.

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

2. Substitua `[self refresh]` em `viewDidLoad` pelo seguinte:

        [self loginAndGetData];

3. Pressione **Executar** para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!---HONumber=Oct15_HO3-->