7. Clique com o botão direito do mouse no projeto do aplicativo de API no **Gerenciador de Soluções** e selecione **Publicar** para reabrir a caixa de diálogo de publicação. O perfil de publicação que você criou anteriormente deve estar pré-selecionado. 

9. Clique em **Publicar** para iniciar o processo de implantação.

	![Implantando o aplicativo de API](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	A janela **Atividade do Serviço de Aplicativo do Azure** mostra o andamento da implantação.

	![Notificação de status na janela Atividade do Serviço de Aplicativo do Azure](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	Durante esse processo de implantação, o Visual Studio tentará reiniciar automaticamente o *gateway*. O gateway é um aplicativo Web que lida com as funções administrativas de todos os aplicativos de API em um grupo de recursos, e precisa ser reiniciado para reconhecer alterações na definição de API do aplicativo de API ou no arquivo *apiapp.json*.
 
	Se você usar outro método para implantar um aplicativo de API ou se o Visual Studio não conseguir reiniciar o gateway, talvez seja necessário reiniciar o gateway manualmente. As etapas a seguir explicam como fazer isso.

1. Em seu navegador, vá para o [Portal de visualização do Azure](https://portal.azure.com).

2. Navegue até a folha do **aplicativo de API** para o aplicativo de API que você implantou.

	Para saber mais sobre a folha do **aplicativo de API** e como encontrá-lo, consulte [Gerenciar aplicativos de API](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Clique no link **Gateway**.

3. Na folha **Gateway**, clique em **Reiniciar**.

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=Oct15_HO3-->