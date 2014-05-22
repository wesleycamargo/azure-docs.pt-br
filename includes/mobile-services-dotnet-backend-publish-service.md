

Depois de testar o aplicativo da Windows Store no serviço móvel local, o estágio final deste tutorial é publicar o serviço móvel no Azure e executar o aplicativo no serviço ativo.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto do serviço móvel e clique em **Publicar**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)

	Isso exibirá a caixa de diálogo Publicar na Web.

2. Clique em **Importar**, clique em **Procurar**, navegue até o local em que salvou o arquivo de perfil de publicação anteriormente, selecione o arquivo de perfil de publicação e clique em **OK**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-import-profile.png)

	Isso carrega as informações necessárias pelo Visual Studio para publicar o serviço móvel no Azure. 
	
	<div class="dev-callout"><strong>Observação sobre segurança</strong> <p>Depois de importar o perfil de publicação, considere a possibilidade de excluir o arquivo baixado pois ele contém informações que podem ser usadas por outras pessoas para acessar seus serviços.</p></div>

3. Clique em **Validar a conexão** para verificar se a publicação está configurada corretamente e, em seguida, clique em **Publicar**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Quando a publicação for bem-sucedida, você verá novamente a página de confirmação de que o serviço móvel está ativo e em execução, desta vez no Azure.


