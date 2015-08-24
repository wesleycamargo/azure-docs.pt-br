<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao ThousandEyes | Microsoft Azure" description="Saiba como usar o ThousandEyes com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao ThousandEyes
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529081).
  
O objetivo deste tutorial é mostrar como configurar o logon único entre o AAD (Active Directory do Azure) e o ThousandEyes.
  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do ThousandEyes com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AAD aos quais você atribui acesso ao ThousandEyes poderão fazer o logon único no aplicativo no site da sua empresa ThousandEyes (serviço provedor iniciado pelo logon) ou usando o Introdução ao Painel de Acesso.

1.  Habilitando a integração de aplicativos com o ThousandEyes
2.  [Configurando o logon único](https://msdn.microsoft.com/library/eae8a61c-8e48-4f15-974d-867adbaf40ca#configuresso)
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Cenário")

##Habilitando a integração de aplicativos com o ThousandEyes
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ThousandEyes.

###Para habilitar a integração de aplicativos com o ThousandEyes, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ThousandEyes**.

    ![Galeria de aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ThousandEyes** e clique em **Concluir** para adicionar o aplicativo.

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##Configurando o logon único
  
Esta descreve como permitir que os usuários se autentiquem no ThousandEyes com a respectiva conta do Active Directory do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **ThousandEyes**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no ThousandEyes**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configurar o logon único")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de entrada do ThousandEyes**, digite a URL que os usuários estão usando para entrar no seu aplicativo ThousandEyes (por exemplo, "**https://app.thousandeyes.com/login/sso*"), e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no ThousandEyes**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, entre em seu site de empresa **ThousandEyes** como um administrador.

6.  No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configurações")

7.  Clique em **Conta**

    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Conta")

8.  Clique na guia **Segurança e Autenticação**.

    ![Segurança e Autenticação](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Segurança e Autenticação")

9.  Na seção de **Configurações de Logon Único**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Configurar Logon Único")

    1.  Selecione **Habilitar Logon Único**.
    2.  No portal do Microsoft Azure, na página de diálogo **Configurar logon único no ThousandEyes**, copie o valor da **URL do logon remoto** e, em seguida, cole-o na caixa de texto **URL da página de logon**.
    3.  No portal do Microsoft Azure, na página de diálogo **Configurar logon único no ThousandEyes**, copie o valor da **URL do logoff remoto** e, em seguida, cole-o na caixa de texto **URL da página de logoff**.
    4.  No portal do Microsoft Azure, na página de diálogo **Configurar logon único no ThousandEyes**, copie o valor da **URL do emissor** e, em seguida, cole-o na caixa de texto **Emissor do provedor de identidade**.
    5.  Em **Certificado do provedor de identidade**, clique em **Escolher arquivo** e carregue o certificado que você baixou do portal do Microsoft Azure.
    6.  Clique em **Salvar**.

10. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no ThousandEyes, eles devem ser provisionados no ThousandEyes. No caso do ThousandEyes, o provisionamento é uma tarefa manual.

###Para provisionar uma conta de usuário no ThousandEyes, execute as seguintes etapas:

1.  Faça logon em seu site de empresa ThousandEyes como um administrador.

2.  Clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configurações")

3.  Clique em **Conta**.

    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Conta")

4.  Clique na guia **Contas e Usuários**.

    ![Contas e Usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Contas e Usuários")

5.  Na seção **Adicionar Usuários e Contas**, execute as seguintes etapas:

    ![Adicionar contas de usuário](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")

    1.  Digite o **Nome**, **Email** e outros detalhes da conta válida do Active Directory do Azure, que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Adicionar Novo Usuário à Conta**.

        >[AZURE.NOTE]O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao ThousandEyes, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ThousandEyes**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->