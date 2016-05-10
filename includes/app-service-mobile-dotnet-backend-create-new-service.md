1. Faça logon no [Portal do Azure].

2. Clique em **+NOVO** > **Web + Móvel** > **Aplicativo Móvel**, então forneça um nome para o back-end do Aplicativo Móvel.

3. Para o **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo (usando o mesmo nome que o aplicativo).
 
4. Para o **Plano de Serviço de Aplicativo**. selecione um plano de Serviço de Aplicativo ou [crie um novo plano](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Clique em **Criar**. Isso cria o back-end de Aplicativo Móvel. As configurações de plano de Serviço de Aplicativo determinam o [local, os recursos, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados ao seu aplicativo.
	
6. Na folha **Configurações** do novo back-end do Aplicativo Móvel, clique em **Início rápido** > sua plataforma de aplicativo cliente > **Conectar um banco de dados**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Na folha **Adicionar conexão de dados**, clique em **Banco de Dados SQL** > **Criar um novo banco de dados**, digite o **Nome** do banco de dados, escolha uma camada de preço e clique em **Servidor**. Você pode reutilizar este novo banco de dados. Se você já tiver um banco de dados no mesmo local, poderá escolher **Usar um banco de dados existente**. O uso de um banco de dados em um local diferente não é recomendado devido aos custos adicionais de largura de banda e à alta latência.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Na folha **Novo servidor**, digite um nome de servidor exclusivo no campo **Nome do servidor**, forneça um logon e uma senha, marque **Permitir que os serviços do Azure acessem o servidor** e clique em **OK**. Isso cria o novo banco de dados.

9. Novamente na folha **Adicionar conexão de dados**, clique em **Cadeia de conexão**, digite os valores de logon e de senha para seu banco de dados e clique em **OK**. Aguarde alguns minutos para que o banco de dados seja implantado com êxito antes de continuar.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->