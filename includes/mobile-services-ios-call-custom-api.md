
<h2><a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada</h2>

1. Crie um botão para que você possa clicar nele para chamar a API personalizada. Arraste um **Botão Rect Redondo** da **Biblioteca de Objetos** localizada na parte inferior do painel **Utilitários** e coloque-o abaixo ou ao lado do campo de texto. Clique duas vezes para adicionar o texto **"Tudo"**. 

	Isso adiciona um novo botão **"Tudo"**.

2. Abra o arquivo de código **QSTodoService.m**, localize o método  `refreshDataOnSuccess` e verifique se ele contém o seguinte código:

		- (void)refreshDataOnSuccess:(QSCompletionBlock)completion
		{		   
		    // Create a predicate that finds items where complete is false
		    NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
		    
		    // Query the TodoItem table and update the items property with the results from the service
		    [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		    {
		        [self logErrorIfNotNil:error];
		        
		        items = [results mutableCopy];
		        
		        // Let the caller know that we finished
		        completion();
		    }];		    		    		    
		}

	Isso filtra os itens de forma que os itens concluídos não sejam retornados pela consulta.

3. Agora é momento de se conectar ao objeto para exibir o código-fonte do controlador. **Clique mantendo Control pressionado** no novo botão **"Tudo"** e arraste o mouse para antes da linha `@end` em **QSTodoListViewController.h**. Conecte o objeto a uma nova **Ação** chamada  `onCompleteAll` em **QSTodoListViewController**. Xcode inserirá automaticamente a seguinte linha antes da linha `@end`:

		   - (IBAction)onCompleteAll:(id)sender;

4. O objetivo desse método  `onCompleteAll` é identificar o evento Click do novo botão. Ele invoca um novo método  `completeAll` que adicionaremos à nossa classe personalizada, que acaba enviando uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros. Edite **QSTodoListViewController.m** para adicionar a seguinte implementação antes da linha `@end`:

		   - (IBAction)onCompleteAll:(id)sender {
		    [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
		     {
		         if (error)
		         {
		             NSString* errorMessage = @"There was a problem! ";
		             errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Error!"
		                                     message:errorMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         } else {
		             NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];					 
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Success!"
		                                     message:successMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         }
		     }];
  		   }

5. Observe que o código acima se refere a um novo método  `completeAll` que ainda não foi definido em **QSTodoService**. Edite **qstodoservice.h** e adicione a seguinte linha antes da linha `@end`:

		- (void) completeAll:(MSAPIBlock)completion;

6. Adicione a implementação correspondente de  `completeAll` em **QSTodoService.m** antes da linha `@end`. O iOS é semelhante a JavaScript, pois não dá suporte à serialização de JSON de tipos arbitrários. Assim, ele tem uma API simples para invocar APIs personalizadas também, consistindo no método  `invokeAPI`. 

		- (void) completeAll:(MSAPIBlock)completion
		{
		    [self.client
		     invokeAPI:@"completeall"
		     body:nil
		     HTTPMethod:@"POST"
		     parameters:nil
		     headers:nil
		     completion:completion ];
		}

## <a name="test-app"></a>Testar o aplicativo

1. Em Xcode, selecione um emulador no qual implantar (iPhone ou iPad), pressione o botão **Executar** (ou a tecla **Command+R**) para recompilar o projeto e iniciar o aplicativo. Isso executa seu cliente de Serviços Móveis do Microsoft Azure, criado com o SDK do iOS, que consulta itens em seu serviço móvel.

2. Digite o texto na caixa de texto e clique no botão **+**. Isso envia um novo item como uma inserção no serviço móvel.

3. Repita a etapa anterior até que você tenha adicionado vários itens à lista.

4. Toque o botão **Tudo**. Uma caixa de alerta é exibida indicando o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

  	![](./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png)

<!--HONumber=42-->
