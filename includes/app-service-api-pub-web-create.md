1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Publicar**. 

	![Opção de menu de publicação do projeto](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Clique na guia **Perfil** e clique em **Aplicativos de API do Microsoft Azure (Visualização)**.

	![Caixa de diálogo Publicar na Web](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Clique em **Novo** para provisionar um novo Aplicativo de API em sua assinatura do Azure.

	![Selecione a caixa de diálogo Serviços de API Existentes](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. Na caixa de diálogo **Criar um Aplicativo de API**, digite o seguinte:

	- Para **Nome de Aplicativo de API**, insira o nome que você está usando neste tutorial. 
	- Se você tiver várias assinaturas do Azure, selecione a que deseja usar.
	- Em **Plano de Serviço de Aplicativo**, selecione entre seus planos de Serviço de Aplicativo existentes ou selecione **Criar novo Plano de Serviço de Aplicativo** e digite o nome do novo plano. 
	- Em **Grupo de Recursos**, selecione entre os grupos de recursos existentes ou selecione **Criar novo grupo de recursos** e digite um nome. 
	- Em **Nível de Acesso**, selecione **Disponível para Qualquer Pessoa**. Você pode restringir o acesso mais tarde no Portal de visualização do Azure.
	- Em **Região**, selecione uma região próxima de você.  

	![Configurar a caixa de diálogo de aplicativo Web do Microsoft Azure](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Clique em **OK** para criar o Aplicativo de API em sua assinatura.

	Como esse processo pode levar alguns minutos, o Visual Studio exibe uma caixa de diálogo de confirmação.

6. Clique em **OK** na caixa de diálogo de confirmação.
 
	O processo de provisionamento cria o grupo de recursos e o Aplicativo de API em sua assinatura do Azure. O Visual Studio mostra o andamento na janela **Atividade do Serviço de Aplicativo do Azure**.

	![Notificação de status pela janela de atividade do Serviço de Aplicativo do Azure](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=August15_HO8-->