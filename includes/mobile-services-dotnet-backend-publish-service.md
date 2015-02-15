

Depois de testar o aplicativo cliente no serviço móvel local, o estágio final deste tutorial é publicar o serviço móvel no Azure e executar o aplicativo no serviço dinâmico.

>[AZURE.NOTE]Este procedimento mostra como publicar seu serviço móvel usando ferramentas do Visual Studio. Também é possível publicar seu serviço móvel de backend .NET usando o controle do código-fonte. Para obter mais informações, consulte [Armazenar código de projeto no controle do código-fonte](/pt-br/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/).

1. No Gerenciador de Soluções, clique com o botão direito no projeto de serviço móvel, clique em **Publicar**, em seguida, na caixa de diálogo **Publicar Web**, clique em **Serviços Móveis do Azure**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)
	
2. Entre usando as credenciais de sua conta do Azure, selecione seu serviço em **Serviços Móveis Existentes** e clique em **OK**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

	O Visual Studio baixa suas configurações de publicação diretamente do Azure.

	>[AZURE.NOTE]O Visual Studio armazena suas credenciais do Azure até que você saia explicitamente.

3. Clique em **Validar a conexão** para verificar se a publicação está configurada corretamente e, em seguida, clique em **Publicar**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Quando a publicação for bem-sucedida, você verá novamente a página de confirmação de que o serviço móvel está ativo e em execução, desta vez no Azure.


<!--HONumber=42-->
