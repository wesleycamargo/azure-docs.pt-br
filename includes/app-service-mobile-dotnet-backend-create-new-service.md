1. Faça logon no [Portal do Azure].

2. No canto superior esquerdo da janela, clique no botão **+NOVO** > **Web + Celular** > **Aplicativo Móvel** e forneça um nome ao back-end do Aplicativo Móvel.

3. Na caixa **Grupo de Recursos**, selecione um grupo de recursos existente. Se você não tiver grupos de recursos, insira o mesmo nome do aplicativo.
 
	Neste ponto, o plano de Serviço de Aplicativo padrão é selecionado, que está na [camada Standard](https://azure.microsoft.com/pricing/details/app-service/). As configurações de plano de Serviço de Aplicativo determinam o local, os recursos, o custo e os recursos de computação associados ao seu aplicativo. Você pode selecionar outro plano de Serviço de Aplicativo ou criar um novo. Para saber mais sobre planos de Serviços de Aplicativos e como criar um novo plano em um tipo de preço e em seu local desejado, confira [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

4. Use o plano de Serviço de Aplicativo padrão, selecione um plano diferente ou [crie um novo plano](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan) e clique em **Criar**.
	
	Isso cria o back-end de Aplicativo Móvel. Posteriormente, você implantará seu projeto de servidor nesse back-end. O provisionamento de um back-end de Aplicativo Móvel pode levar vários minutos; a folha **Configurações** para o back-end do Aplicativo Móvel é exibida ao concluir. Antes de usar o back-end de Aplicativo Móvel, você também deverá definir uma conexão a um repositório de dados.

    > [AZURE.NOTE] Como parte deste tutorial, você criará um novo servidor e uma nova instância do Banco de Dados SQL. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Se já houver um banco de dados no mesmo local do novo back-end do aplicativo móvel, você poderá escolher **Usar um banco de dados existente** e selecionar esse banco de dados. O uso de um banco de dados em um local diferente não é recomendado devido a latências maiores e aos custos adicionais de largura de banda. Outras opções de armazenamento de dados estão disponíveis.

6. Na folha **Configurações** do novo back-end do Aplicativo Móvel, clique em **Início rápido** > sua plataforma de aplicativo cliente > **Conectar um banco de dados**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Na folha **Adicionar conexão de dados**, clique em **Banco de Dados SQL** > **Criar um novo banco de dados**, digite o **Nome** do banco de dados, escolha uma camada de preço e clique em **Servidor**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Na folha **Novo servidor**, digite um nome de servidor exclusivo no campo **Nome do servidor**, forneça um **Logon de administrador do servidor** seguro e uma **senha**, verifique se **Permitir que os serviços do azure acessem o servidor** está marcado e clique em **OK** duas vezes. Isso cria o novo banco de dados e o servidor.

10. Novamente na folha **Adicionar conexão de dados**, clique em **Cadeia de conexão**, digite os valores de logon e de senha para seu banco de dados e clique em **OK** duas vezes.

	A criação do banco de dados pode levar alguns minutos. Use a área **Notificações** para monitorar o progresso da implantação. Você não conseguirá prosseguir até o banco de dados tiver sido implantado com êxito.


<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0224_2016-->