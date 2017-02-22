## <a name="generate-an-api-app-client"></a>Gerar um cliente de aplicativo de API
As ferramentas do aplicativo de API no Visual Studio facilitam a geração do código em C# que chama os aplicativos de API do Azure por meio da área de trabalho, armazenamento e aplicativos móveis. 

1. No Visual Studio, abra a solução que contém o aplicativo de API do tópico [Criar aplicativo de API](../articles/app-service-api/app-service-dotnet-create-api-app.md) . 
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e selecione o **Adicionar** > **Novo Projeto**.
   
    ![Adicionar um novo projeto](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)
3. Na caixa de diálogo **Adicionar Novo Projeto** , execute as seguintes etapas:
   
   1. Selecione a categoria **Área de Trabalho do Windows** .
   2. Selecione o modelo do projeto **Aplicativo de Console** .
   3. Nomeie o projeto.
   4. Clique em **OK** para gerar o novo projeto na solução existente.
      
      ![Adicionar um novo projeto](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)
4. Clique com o botão direito do mouse no projeto de aplicativo de console recém-criado e selecione **Adicionar** > **Cliente de aplicativo de API do Azure**. 
   
    ![Adicionar um novo cliente](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
5. Na caixa de diálogo **Adicionar Cliente de Aplicativo de API do Microsoft Azure** , execute as seguintes etapas: 
   
   1. Selecione a opção **Baixar** . 
   2. Na lista suspensa, selecione o aplicativo de API que você criou anteriormente. 
   3. Clique em **OK**. 
      
      ![Tela de geração](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)
      
      O assistente vai baixar o arquivo de metadados de API e gerar uma interface digitada para invocar o aplicativo de API.
      
      ![Geração acontecendo](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)
      
      Após a conclusão da geração de código, você verá uma nova pasta no Gerenciador de Soluções, com o nome do aplicativo de API. Esta pasta contém o código que implementa os cliente e modelos de dados. 
      
      ![Geração completa](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)
6. Abra o arquivo **Program.cs** na raiz do projeto e substitua o método **Main** pelo código a seguir: 
   
        static void Main(string[] args)
        {
            var client = new ContactsList();
   
            // Send GET request.
            var contacts = client.Contacts.Get();
            foreach (var c in contacts)
            {
                Console.WriteLine("{0}: {1} {2}",
                    c.Id, c.Name, c.EmailAddress);
            }
   
            // Send POST request.
            client.Contacts.Post(new Models.Contact
            {
                EmailAddress = "lkahn@contoso.com",
                Name = "Loretta Kahn",
                Id = 4
            });
   
            Console.WriteLine("Finished");
            Console.ReadLine();
        }

## <a name="test-the-api-app-client"></a>Testar o cliente do aplicativo de API
Depois que o aplicativo de API tiver sido codificado, é hora de testar o código.

1. Abra o **Gerenciador de Soluções**.
2. Clique com o botão direito do mouse no aplicativo de console que você criou na seção anterior.
3. No menu de contexto do aplicativo de console, selecione **Depurar > Iniciar nova instância**. 
4. Uma janela de console deve abrir e exibir todos os contatos. 
   
    ![Aplicativo de console em execução](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)
5. Pressione **Enter** para descartar a janela de console.          



<!--HONumber=Jan17_HO3-->


