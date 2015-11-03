

Siga estas etapas para criar um novo back-end de aplicativo móvel.

1. Faça logon no [Portal do Azure].

2. Na parte superior esquerda da janela, clique no botão **+NOVO** > **Web + Celular** > **Aplicativo Móvel**, em seguida, forneça um nome ao back-end do aplicativo móvel.

3. Na caixa **Grupo de Recursos**, digite o mesmo nome do aplicativo.

4. O plano Serviço de Aplicativo Padrão será selecionado. Para alterar seu plano de Serviço do Aplicativo, clique em Plano de Serviço de Aplicativo > **+ Criar Novo**. Forneça um nome ao novo Plano de Serviço de Aplicativo e selecione um local apropriado. Clique em Camada de Preços e selecione uma camada de preços apropriada para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar a camada de preços, clique no botão **Selecionar**. De volta à folha **Plano do Serviço de Aplicativo**, clique em **OK**.

5. Clique em **Criar**. Isso cria um back-end de aplicativo móvel onde você, mais tarde, implantará o projeto do servidor. O provisionamento de um back-end de aplicativo móvel pode levar alguns minutos. Depois que o back-end do aplicativo móvel é provisionado, o portal abre a folha **Configurações** para o back-end do aplicativo móvel. Na próxima etapa, você criará um novo banco de dados SQL.

    > [AZURE.NOTE]Como parte deste tutorial, você criará um novo servidor e uma nova instância do Banco de Dados SQL. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Se já houver um banco de dados no mesmo local do novo back-end do aplicativo móvel, você poderá escolher **Usar um banco de dados existente** e selecionar esse banco de dados. O uso de um banco de dados em um local diferente não é recomendado devido a latências maiores e aos custos adicionais de largura de banda.

6. No novo back-end do aplicativo móvel, clique em **Configurações** > **Aplicativo Móvel** > **Dados** > **+ Adicionar**.

7. Na folha **Adicionar conexão de dados**, clique em **Banco de Dados SQL - Definir configurações necessárias** > **Criar um novo banco de dados**. Digite o nome do novo banco de dados no campo **Nome**.

8. Clique em **Servidor**. Na folha **Novo servidor**, insira um nome de servidor exclusivo no campo **Nome do servidor** e forneça um **Logon de administração de servidor** e **Senha**adequados. Verifique se **Permitir que os serviços do Azure acessem o servidor** está marcado. Clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

9. Na folha **Novo banco de dados**, clique em **OK**.

10. De volta à folha **Adicionar conexão de dados**, selecione **Cadeia de conexão**, insira o logon e a senha que você forneceu ao criar o banco de dados. Se você usar um banco de dados existente, forneça as credenciais de logon desse banco de dados. Depois de inserir, clique em **OK**.

11. De volta novamente à folha **Adicionar conexão de dados**, clique em **OK** para criar o banco de dados.

A criação do banco de dados pode levar alguns minutos. Use a área **Notificações** para monitorar o progresso da implantação. Não siga adiante enquanto o banco de dados não tiver sido implantado com êxito.

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO1-->