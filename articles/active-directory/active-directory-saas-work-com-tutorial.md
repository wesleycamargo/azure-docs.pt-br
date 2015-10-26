<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Work.com | Microsoft Azure" description="Saiba como usar o Work.com com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Work.com
  
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529836).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Work.com. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Work.com com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AAD aos quais você atribui acesso ao Work.com poderão fazer o logon único no aplicativo no site da sua empresa Work.com (serviço provedor iniciado pelo logon) ou usando o [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Work.com
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-work-com-tutorial/IC794105.png "Cenário")

##Habilitando a integração de aplicativos para o Work.com
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Work.com.

###Para habilitar a integração de aplicativos com o Work.com, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-work-com-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-work-com-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Work.com**.

    ![Galeria de Aplicativos](./media/active-directory-saas-work-com-tutorial/IC794106.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Work.com** e clique em **Concluir** para adicionar o aplicativo.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Work.com com sua conta do AD do Azure usando federação baseada em protocolo de SAML. Como parte desse procedimento, é necessário carregar um certificado para o Work.com.

>[AZURE.NOTE]Para configurar o logon único, você ainda precisa definir um nome de domínio personalizado Work.com. Você precisa definir pelo menos um nome de domínio, testá-lo e implantá-lo em toda a sua organização.

###Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon no seu locatário Work.com como administrador.

2.  Vá para **Configuração**.

    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

3.  No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e, em seguida, clique em **Meu Domínio** para abrir a página **Meu Domínio**.

    ![Meu Domínio](./media/active-directory-saas-work-com-tutorial/IC767825.png "Meu Domínio")

4.  Para verificar se seu domínio foi configurado corretamente, certifique-se de que está em “**Etapa 4 Implantada para Usuários**” e revise “**Minhas Configurações de Domínio**”.

    ![Domínio implantado para o usuário](./media/active-directory-saas-work-com-tutorial/IC784377.png "Domínio implantado para o usuário")

5.  Em uma janela de navegador da Web diferente, faça logon no seu portal do Azure.

6.  Na página de integração do aplicativo **Work.com **, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Configurar o logon único](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar o logon único")

7.  Na página **Como você deseja que os usuários façam logon no Work.com **, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar o logon único")

8.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon do Work.com**, digite a URL usada pelos seus usuários para fazer logon no seu aplicativo Work.com (p. ex.: “ **http://company.my.salesforce.com*”)e, em seguida, clique em **Avançar**:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar a URL do Aplicativo")

9.  Na página **Configurar logon único no Work.com**, para baixar seu certificado, clique em **Baixar certificado** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar o logon único")

10. Faça logon no seu locatário Work.com.

11. Vá para **Configuração**.

    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

12. Expanda o menu **Controles de Segurança** e clique em **Configurações de Logon Único**.

    ![Configurações de Logon Único](./media/active-directory-saas-work-com-tutorial/IC794113.png "Configurações de Logon Único")

13. Na página de caixa de diálogo **Configurações de Logon Único**, execute as seguintes etapas:

    ![SAML Habilitado](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Habilitado")

    1.  Selecione **SAML Habilitado**.
    2.  Clique em **Novo**.

14. Na seção de **Configurações de Logon Único de SAML**, execute as seguintes etapas:

    ![Configuração de Logon Único do SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "Configuração de logon único do SAML")

    1.  Na caixa de texto **Nome**, digite um nome para sua configuração.  

        >[AZURE.NOTE]Fornecer um valor para **Nome** preenche automaticamente a caixa de texto **Nome da API**.

    2.  No portal do Azure, na página de diálogo **Configurar logon único no Work.com**, copie o valor da **URL do Emissor** e, em seguida, cole-o na caixa de texto **Emissor**.
    3.  Para carregar o certificado baixado, clique em **Pesquisar**.
    4.  Na caixa de texto **Id da Entidade**, digite ****https://salesforce-work.com**.
5.  Para o **Tipo de Identidade SAML** selecione **Assertion contém a ID de Federação do objeto User**.
    6.  Para **Local de Identidade de SAML**, selecione **A identidade está no elemento NameIdentfier da instrução de assunto**
    7.  No portal do Azure, na página de diálogo **Configurar logon único no Work.com**, copie o valor da **URL de Logon Remoto** e, em seguida, cole-o na caixa de texto **URL do Login do Provedor de Identidade**.
    8.  No portal do Azure, na página de diálogo **Configurar logon único no Work.com**, copie o valor da **URL de Logout Remoto** e, em seguida, cole-o na caixa de texto **URL do logout do Provedor de Identidade**.
    9.  Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **HTTP Post**.
    10. Clique em **Salvar**.

15. No painel de navegação à esquerda, no seu portal Work.com, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e, em seguida, clique em **Meu Domínio** para abrir a página **Meu Domínio**.

    ![Meu Domínio](./media/active-directory-saas-work-com-tutorial/IC794115.png "Meu Domínio")

16. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.

    ![Identidade visual da página de logon](./media/active-directory-saas-work-com-tutorial/IC767826.png "Identidade visual da página de logon")

17. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.

    ![Identidade visual da página de logon](./media/active-directory-saas-work-com-tutorial/IC784366.png "Identidade visual da página de logon")

18. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para que os usuários do Active Directory do Azure possam entrar, eles devem ser provisionados para Work.com. No caso do Work.com, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon no site da sua empresa do Work.com como um administrador.

2.  Vá para **Configuração**.

    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

3.  Vá para **Gerenciar Usuários > Usuários**.

    ![Gerenciar Usuários](./media/active-directory-saas-work-com-tutorial/IC784369.png "Gerenciar Usuários")

4.  Clique em **Novo Usuário**.

    ![Todos os Usuários](./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos os usuários")

5.  Na seção Editar Usuário, execute as seguintes etapas:

    ![Editar usuário](./media/active-directory-saas-work-com-tutorial/IC794118.png "Editar usuário")

    1.  Digite os atributos **Sobrenome**, **Alias**, **Email**, **Nome de Usuário** e **Apelido** de uma conta válida do Active Directory do Azure, que você deseja provisionar nas caixas de texto relacionadas.
    2.  Selecione **Função**, **Licença de Usuário** e **Perfil**.
    3.  Clique em **Salvar**.  

        >[AZURE.NOTE]O titular da conta do Active Directory do Azure receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Work.com ou APIs fornecidas pelo Work.com para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao Work.com, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do Work.com, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-work-com-tutorial/IC794119.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-work-com-tutorial/IC767830.png "Sim")
  
Agora, você deveria aguardar 10 minutos e verificar se a conta foi sincronizada com o Work.com.
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->