* Abra **QSTodoListViewController.m** e adicione o método a seguir. Altere *facebook* para *contadamicrosoft*, *twitter*, *google* ou *activedirectorydowindowsazure* se não estiver usando o Facebook como seu provedor de identidade.

```
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
```

* Substitua `[self refresh]` em `viewDidLoad` pelo seguinte:

```
        [self loginAndGetData];
```

* Pressione **Executar** para iniciar o aplicativo e, em seguida, faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!---HONumber=Oct15_HO3-->