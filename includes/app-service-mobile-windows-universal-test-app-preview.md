
Volte ao Visual Studio e selecione o projeto de aplicativo de cliente de código compartilhado (ele é nomeado como `<your app name>.Shared`)

1. Expanda o arquivo **App.xaml** e, em seguida, abra o arquivo **App.xaml.cs**. Localize a declaração do membro `MobileService`, que é inicializado com uma URL localhost. Comente essa declaração (com `CTRL+K,CTRL+C`) e remova o comentário da declaração (`CTRL+K,CTRL+U`) que se conecta ao seu serviço hospedado:
   
        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);
   
        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );
2. Pressione a tecla F5 para recompilar o projeto e iniciar o aplicativo da Windows Store, o qual deve ser seu projeto de inicialização padrão.
3. No aplicativo, digite um texto significativo, como *Concluir o tutorial*, na caixa de texto **Inserir um TodoItem** e, em seguida, clique em **Salvar**.
   
    ![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)
   
    Isso envia uma solicitação POST para o novo back-end do aplicativo móvel hospedado no Azure.
4. Interrompa a depuração e altere a projeto de inicialização padrão na solução universal do Windows para o aplicativo da Loja do Windows Phone (clique com o botão direito do mouse no projeto `<your app name>.WindowsPhone` e clique em **Definir como projeto de inicialização**) e pressione F5 novamente.
   
    ![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)
   
    Observe que os dados salvos da etapa anterior são carregados por meio do aplicativo móvel após o aplicativo do Windows ser iniciado.

<!---HONumber=Oct15_HO3-->