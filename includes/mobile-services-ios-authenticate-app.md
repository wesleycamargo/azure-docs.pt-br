

1. Abra o arquivo de projeto QSTodoListViewController.m e, no método **viewDidLoad**, remova o seguinte código que recarrega os dados na tabela:

        [self refresh];

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

    > [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para **loginWithProvider** acima para um dos seguintes: _microsoftaccount_, _facebook_, _twitter_, _google_ ou _windowsazureactivedirectory_.
		
3. Pressione o botão **Executar** para compilar o projeto, iniciar o aplicativo no emulador do iPhone e fazer logon com o provedor de identidade escolhido.

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.
\<!--HONumber=42-->
