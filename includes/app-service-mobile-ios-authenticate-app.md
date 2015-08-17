

1. Abra **QSTodoListViewController.m** e adicione o seguinte método:


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


    > [AZURE.NOTE]Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado ao **loginWithProvider**. Os valores com suporte são: _microsoftaccount_, _facebook_, _twitter_, _google_ ou _windowsazureactivedirectory_.


2. Modifique `viewDidLoad` substituindo `[self refresh]` no final pelo seguinte:

        [self loginAndGetData];

3. Pressione **Executar** para iniciar o aplicativo e, em seguida, faça logon com o provedor de identidade escolhido por você. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!---HONumber=August15_HO6-->