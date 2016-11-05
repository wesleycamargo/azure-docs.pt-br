Nesta seção, você adicionará dois novos usuários a seu diretório, juntamente com o novo grupo Vendas. Será concedida, a um desses usuários, associação ao grupo Vendas. Ao outro usuário, não será concedida a associação.

### Criar os usuários
1. No [portal clássico do Azure](https://manage.windowsazure.com), navegue até o diretório que você configurou anteriormente para autenticação quando concluiu o tutorial para adicionar a autenticação ao aplicativo.
2. Clique em **Usuários** na parte superior da página. Clique no botão **Adicionar Usuário**, na parte inferior. 
3. Preencha os diálogos do novo usuário para criar um usuário chamado **Bob**. Anote a senha temporária para esse usuário. 
4. Crie outro usuário chamado **Dave**. Anote a senha temporária para esse usuário.
5. Os novos usuários devem parecer similares quanto ao que é mostrado abaixo.
   
    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)

### Criar o grupo Vendas
1. Na página do diretório, clique em **Grupos** na parte superior da página. Clique no botão **Adicionar Grupo**, na parte inferior. 
2. Insira **Vendas** como o nome do grupo e pressione o botão de conclusão no diálogo para criar o grupo. 
   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Associar o usuário ao grupo Vendas.
1. Clique em **Grupos** na parte superior da página do diretório. Clique no grupo **Vendas** para ir até a página correspondente. 
2. Na página do grupo Vendas, clique em **Adicionar Membros**. Adicione o usuário chamado **Bob** ao grupo Vendas. O usuário chamado **Dave** não deve ser um membro do grupo.
   
    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)
3. Na página Grupo de vendas, clique em **Propriedades** e, em seguida, copie a **ID de objeto** para o grupo de vendas na parte inferior da página.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

1. Navegue de volta para sua página de configuração do serviço móvel e adicione a ID de objeto como uma configuração de aplicativo chamada **AAD\\_SALES\\_GROUP\\_ID**. Este tutorial usa a ID do objeto do grupo como uma configuração de aplicativo, em vez de procurar a identificação com base no nome do grupo. Isso porque o nome do grupo pode mudar, mas a ID permanece a mesma.
   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!---HONumber=AcomDC_1203_2015-->