
Em seguida, você deve fazer alterações ao se registrar para notificações por push para assegurar que o usuário está autenticado antes da tentativa de registro.

1. No Visual Studio em Gerenciador de Soluções, abra o arquivo de projeto app.xaml.cs e no manipulador de eventos **Application_Launching** comente ou exclua a chamada para o método **AcquirePushChannel**. 
 
2. Altere a acessibilidade do método **AcquirePushChannel** de `private` para `public` e adicione o modificador `static`.

3. Abra o arquivo de projeto MainPage.xaml.cs, e substitua o método **OnNavigatedTo** existente pelo seguinte:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }
<!--HONumber=54-->