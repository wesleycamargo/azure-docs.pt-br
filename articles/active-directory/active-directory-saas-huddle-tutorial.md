<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Huddle | Microsoft Azure" 
    description="Saiba como usar o Huddle com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao Huddle
  
O objetivo deste tutorial é mostrar a integração do Azure ao Huddle. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Huddle
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Huddle poderão fazer logon único no aplicativo em seu site de empresa do Huddle (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Huddle
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Configurar o logon único](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurar o logon único")
##Habilitando a integração de aplicativos para Huddle
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Huddle.

###Para habilitar a integração de aplicativos para o Huddle, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-huddle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-huddle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-huddle-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Huddle**.

    ![Galeria de Aplicativos](./media/active-directory-saas-huddle-tutorial/IC787831.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Huddle** e clique em **Concluir** para adicionar o aplicativo.

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Huddle com a própria conta do Azure AD usando a federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Huddle**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Huddle**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurar o logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Huddle**, digite a URL do seu locatário Huddle usando o padrão "*http://company.huddle.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Huddle**, realize as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurar o logon único")

    1.  Clique em **Baixar certificado** e salve o certificado no computador.
    2.  Copie o valor de **URL do Emissor**, o valor de **URL de SSO do SAML** e o certificado baixado e envie tudo à equipe de suporte do Huddle.

    >[AZURE.NOTE] O logon único precisa ser habilitado pela equipe de suporte do Huddle. Assim que a configuração for concluída, você receberá uma notificação.

5.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no Huddle, eles deverão ser provisionados no Huddle. No caso do Huddle, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Huddle** como administrador.

2.  Clique em **Espaço de trabalho**.

3.  Clique em **Pessoas > Convidar Pessoas**.

    ![Pessoas](./media/active-directory-saas-huddle-tutorial/IC787838.png "Pessoas")

4.  Na seção **Criar novo convite**, realize as seguintes etapas:

    ![Novo Convite](./media/active-directory-saas-huddle-tutorial/IC787839.png "Novo Convite")

    1.  Na lista **Escolha uma equipe para convidar pessoas para participar**, selecione **equipe**.
    2.  Digite o **Endereço de Email** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    3.  Clique em **Convidar**.

    >[AZURE.NOTE] O titular da conta do Azure AD receberá um email com um link de confirmação de conta para que ela se torne ativa.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do Huddle ou as APIs fornecidas pelo Huddle para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Huddle, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Huddle**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-huddle-tutorial/IC787840.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-huddle-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->
