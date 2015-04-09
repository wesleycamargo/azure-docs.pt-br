
O exemplo anterior mostra uma entrada padrão, que requer que o cliente contate tanto o provedor de identidade quanto o Serviço de Aplicativo sempre que o aplicativo for iniciado. Esse método é ineficiente, e seria melhor armazenar em cache o token de autorização retornado pelo Serviço de Aplicativo e tentar usar esse token primeiro, antes de usar uma entrada baseada em provedor.

1. A maneira recomendada para criptografar e armazenar os tokens de autenticação em um cliente iOS é usar o Conjunto de Chaves do iOS. Este tutorial usa o [SSKeychain](https://github.com/soffes/sskeychain) - um wrapper simples em torno do conjunto de chaves do iOS. Siga as instruções na página SSKeychain e adicione-as ao seu projeto. Verifique que a configuração **Habilitar módulos** esteja habilitada nas **Configurações de criação** do projeto (seção **LLVM (máquina virtual de baixo nível) da Apple - Idiomas - Módulos**.)

2. Abra **QSTodoListViewController.m** e adicione o código a seguir:


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. No final do método **viewDidAppear** em **QSTodoListViewController.m**, adicione uma chamada para **saveAuthInfo** logo antes da linha `[self refresh]`. Com esta chamada, apenas armazenamos a ID de usuário e as propriedades do token:

				[self saveAuthInfo];

4. Vamos também carregar a ID de usuário e o token quando o aplicativo for iniciado. No método **viewDidLoad** em **QSTodoListViewController.m**, adicione uma chamada para loadAuthInfo, logo após o **self.todoService** ter sido inicializado.

				[self loadAuthInfo];

<!--HONumber=49-->