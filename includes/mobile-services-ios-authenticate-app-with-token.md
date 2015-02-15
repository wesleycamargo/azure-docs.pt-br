
O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o serviço móvel sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma melhor abordagem é armazenar em cache o token de autorização retornado pelos Serviços Móveis e tentar usá-lo antes de usar a entrada baseada no provedor. 


>[AZURE.NOTE] Você pode armazenar em cache o token emitido pelos Serviços Móveis usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.

1. A maneira recomendada para criptografar e armazenar os tokens de autenticação em um cliente iOS é usar o Conjunto de Chaves. Para isso, criar uma classe KeychainWrapper, copiando o [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) e [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) do [exemplo de LensRocket](https://github.com/WindowsAzure-Samples/iOS-LensRocket). Usamos o KeychainWrapper como o KeychainWrapper definido na documentação da Apple não conta para o ARC (contagem de referência automática).


2. Abra o arquivo de projeto **QSTodoListViewController.m** e adicione o seguinte código:

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. No final do método **viewDidAppear** no **QSTodoListViewController.m**, adicione uma chamada para saveAuthInfo. Com esta chamada, apenas estamos armazenando as propriedades do userId e do token.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Agora que vimos como podemos armazenar em cache o token e a ID do usuário, vejamos como podemos carregá-los assim que o aplicativo iniciar. No método **viewDidLoad** em **QSTodoListViewController.m**, adicione uma chamada para loadAuthInfo, após que o **self.todoService** tiver inicializado. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Se o aplicativo fizer uma solicitação para seu Serviço Móvel que deva passar porque o usuário é autenticado e você recebe uma resposta 401 (erro de não autorizado), significa que o token do usuário que você estiver passando já expirou. No manipulador de conclusão, para cada método que tivermos que interaja com nosso Serviço Móvel, podemos verificar uma resposta 401, ou podemos lidar com a situação em um lugar: o método handleRequest do MSFilter.  Para ver como lidar com este cenário, consulte [esta postagem de blog](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)


<!--HONumber=42-->
