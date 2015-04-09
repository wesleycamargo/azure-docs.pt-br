

1. Abra **QSTodoListViewController.m** e, no método **viewDidLoad**, remova a linha a seguir:

        [atualização automática];

2.	Logo após o método **viewDidLoad**, adicione o seguinte código:  

			- (void)viewDidAppear:(BOOL)animated
			{
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;

            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
			}

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado ao **loginWithProvider**. Os valores para os quais há suporte são: _microsoftaccount_, _facebook_, _twitter_, _google_ ou _windowsazureactivedirectory_.

3. Pressione **Executar** para iniciar o aplicativo e, em seguida, faça logon com o provedor de identidade escolhido por você. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

<!--HONumber=49-->