
O exemplo anterior contata o provedor de identidade e o serviço móvel toda vez que o aplicativo é iniciado. Em vez disso, você pode armazenar em cache o token de autorização e tentar usá-lo primeiro.

* A maneira recomendada para criptografar e armazenar os tokens de autenticação em um cliente iOS é usar o Conjunto de Chaves do iOS. Usaremos o [SSKeychain](https://github.com/soffes/sskeychain) – um wrapper simples em torno do conjunto de chaves do iOS. Siga as instruções na página SSKeychain e adicione-as ao seu projeto. Verifique que a configuração **Habilitar Módulos** está habilitada nas **Configurações de compilação** do projeto (seção **LLVM (máquina virtual de baixo nível) da Apple - Idiomas - Módulos**.)

* Abra o **QSTodoListViewController.m** e adicione o código a seguir:

```
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
```

* Em `loginAndGetData`, modifique o bloco de conclusão de `loginWithProvider:controller:animated:completion:`. Adicione a seguinte linha antes de `[self refresh]` para armazenar a ID do usuário e as propriedades do token:

```
				[self saveAuthInfo];
```

* Vamos carregar a ID do usuário e o token quando o aplicativo for iniciado. Em `viewDidLoad` no **QSTodoListViewController.m**, adicione a linha a seguir logo depois que`self.todoService` for inicializado.

```
				[self loadAuthInfo];
```

<!---HONumber=July15_HO2-->