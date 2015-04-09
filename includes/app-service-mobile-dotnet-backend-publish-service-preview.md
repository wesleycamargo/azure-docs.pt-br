Depois de testar o aplicativo cliente no aplicativo móvel local, o estágio final deste tutorial é publicar o back-end do aplicativo móvel no Azure e executar o aplicativo no serviço em tempo real.

> [AZURE.NOTE] Este procedimento mostra como publicar seu back-end de aplicativo móvel usando ferramentas do Visual Studio. Você também pode publicar seu back-end do .NET usando o controle do código-fonte.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de código do aplicativo móvel (é o nome do aplicativo acrescido da palavra "Serviço") e selecione **Publicar**. 

	![Select publish on the app code project](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. Na caixa de diálogo **Publicar Web**, selecione **Aplicativo Móvel do Azure** como o Destino de Publicação. Na caixa de diálogo que aparece selecione seu aplicativo móvel existente, que será o nome do aplicativo móvel acrescido da palavra "código".

    ![Select an existing web app to publish to](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. Clique em **Validar a conexão** para verificar se a publicação está configurada corretamente e, em seguida, clique em **Publicar**.

	![Publish settings wizard final page](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   Após a publicação ser bem-sucedida, você verá a página de confirmação de que o back-end do aplicativo móvel está ativo e em execução no Azure. A janela de saída do Visual Studio também mostrará êxito.

<!--HONumber=49-->