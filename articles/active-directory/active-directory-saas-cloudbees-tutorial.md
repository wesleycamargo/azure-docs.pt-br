<properties pageTitle="Tutorial: integração do Active Directory do Azure ao CloudBees | Microsoft Azure" description="Saiba como usar o CloudBees com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao CloudBees
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528737).

O objetivo deste tutorial é mostrar a integração do Azure ao CloudBees. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do CloudBees

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu ao CloudBees poderão fazer logon único no aplicativo em seu site de empresa do CloudBees (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/azure/dn308586.aspx)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o CloudBees
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "Cenário")

##Habilitando a integração de aplicativos para o CloudBees

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o CloudBees.

###Para habilitar a integração de aplicativos para o CloudBees, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **CloudBees**.

    ![Galeria de Aplicativos](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **CloudBees** e clique em **Concluir** para adicionar o aplicativo.

    ![Cloudbees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "Cloudbees")

##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no CloudBees com a própria conta no Azure AD usando federação baseada no protocolo SAML. Configurar o logon único para o CloudBees exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **CloudBees**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no CloudBees**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Configurar o logon único")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de Logon do CloudBees**, digite a URL usada pelos seus usuários para fazer logon no CloudBees (por exemplo: “**https://grandcentral.cloudbees.com/login*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no CloudBees**, para baixar seu certificado, clique em **Baixar certificado** e salve o certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do CloudBees como administrador.

6.  No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "Configurações")

7.  Clique na guia **Integração SSO**.

8.  Na seção **Configurar Logon Único do SAML 2.0**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "Configurar Logon Único")

    1.  No portal do Azure AD, na página **Configurar logon único no CloudBees**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de logon remoto**.
    2.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital de certificado existente**.
    
        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    3.  Na caixa de texto **Domínios de autenticação**, digite o domínio de sua empresa.
    4.  Clique em **Salvar**.

9.  No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Configurar o logon único")

##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no **CloudBees**, eles devem ser provisionados no **CloudBees**. No caso do **CloudBees**, o provisionamento é uma tarefa manual.

###Para provisionar uma conta de usuário no CloudBees, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **CloudBees** como administrador.

2.  Acesse **Editar Usuários**.

    ![Usuários](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "Usuários")

3.  Clique em **Adicionar**.

    ![Adicionar](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "Adicionar")

4.  Na seção **Adicionar Usuário**, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "Adicionar usuário")

    1.  Digite o **Email**, **Nome**, **Sobrenome** e outros atributos da conta válida do Active Directory do Azure, que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Adicionar Usuário**.

        >[AZURE.NOTE]Um email com instruções de logon será enviado ao proprietário da conta.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do CloudBees ou as APIs fornecidas pelo CloudBees para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao CloudBees, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ClickTime**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->