Nesta seção, você adicionará dois novos usuários a seu diretório, juntamente com o novo grupo Vendas. Será concedida, a um desses usuários, associação ao grupo Vendas. Ao outro usuário, não será concedida a associação. 

### Criar os usuários


1. No [Portal de Gerenciamento do Azure], navegue até o diretório que você configurou anteriormente para autenticação quando concluiu o tutorial [Introdução à Autenticação].
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

3. Na página do grupo Vendas, clique em **Configurar**, e observe a **ID de Objeto** para o grupo de vendas. Este tutorial, na realidade, pesquisa a ID de objeto do grupo que usa as Graph APIs. Desse modo, você não precisará da ID. Todavia, em alguns casos, é melhor não utilizar o nome de grupo porque ele pode ser alterado quando a ID permanece a mesma. Se você quiser armazenar a ID do grupo como uma configuração do aplicativo no serviço móvel, ou fazer hard-coding dela em seu código, é aqui que você a encontrará.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)



<!--HONumber=42-->
