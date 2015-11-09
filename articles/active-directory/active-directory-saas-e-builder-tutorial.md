<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao e-Builder | Microsoft Azure" 
    description="Saiba como usar o e-Builder com o Active Directory do Azure para habilitar logon único, o provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: integração do Active Directory do Azure ao e-Builder
  
O objetivo deste tutorial é mostrar a integração do Azure ao e-Builder. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do e-Builder
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao E-Builder poderão fazer logon único no aplicativo em seu site de empresa do E-Builder (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o e-Builder
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Cenário")
##Habilitando a integração de aplicativos para o e-Builder
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o e-Builder.

###Para habilitar a integração de aplicativos para o e-Builder, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **e-Builder**.

    ![Galeria de aplicativos](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **e-Builder** e clique em **Concluir** para adicionar o aplicativo.

    ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no e-Builder com a própria conta no Azure AD usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **E-Builder**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no E-Builder**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do e-Builder**, digite a URL usando o padrão “*https://\<nome-locatário>.e-Builder.com*” e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no E-Builder**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de dados localmente como **c:\\E-BuilderMetaData.xml**.

    ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurar logon único")

5.  Encaminhe esse arquivo de metadados à equipe de suporte do e-Builder. A equipe de suporte precisa configurar o logon único para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar o diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há qualquer item de ação para a configuração do provisionamento de usuário para o e-Builder. Quando um usuário atribuído tentar fazer logon no e-Builder usando o painel de acesso, o e-Builder verificará se o usuário existe. Se ainda não houver uma conta de usuário, ela será automaticamente criada pelo e-Builder.
##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao e-Builder, execute as etapas a seguir:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **e-Builder**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->