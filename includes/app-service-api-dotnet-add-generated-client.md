1. Em **Gerenciador de soluções**, clique com o botão direito no projeto (não na solução) e selecione **Adicionar > Cliente de aplicativo de API do Azure**. 
   
    ![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
2. Na caixa de diálogo **Adicionar cliente de aplicativo de API do Azure**, clique em **Baixar a partir do aplicativo de API do Azure**.
3. Na lista suspensa, selecione o aplicativo de API que você deseja chamar.
4. Clique em **OK**.
   
    ![Tela de geração](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)
   
    O assistente baixa o arquivo de metadados de API e gera uma interface digitada para chamar o aplicativo de API.
   
    ![Geração acontecendo](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)
   
    Após a conclusão da geração de código, você vê uma nova pasta **Gerenciador de soluções**, com o nome do aplicativo de API. Esta pasta contém o código que implementa os modelos de dados e classes de cliente.
   
    ![Geração completa](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=Oct15_HO3-->