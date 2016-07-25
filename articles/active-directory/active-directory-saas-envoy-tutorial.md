<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Envoy | Microsoft Azure" 
    description="Saiba como usar o Envoy com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: integração do Active Directory do Azure ao Envoy
  
O objetivo deste tutorial é mostrar a integração do Azure ao Envoy. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Envoy
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Envoy poderão fazer logon único no aplicativo em seu site de empresa do Envoy (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Envoy
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-envoy-tutorial/IC776759.png "Cenário")
##Habilitando a integração de aplicativos para o Envoy
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Envoy.

###Para habilitar a integração de aplicativos para o Envoy, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-envoy-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-envoy-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-envoy-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Envoy**.

    ![Galeria de aplicativos](./media/active-directory-saas-envoy-tutorial/IC776760.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Envoy** e clique em **Concluir** para adicionar o aplicativo.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no Envoy com a própria conta no Azure AD usando federação baseada no protocolo SAML. Configurar o logon único para o Envoy exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos **Envoy**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Habilitar logon único](./media/active-directory-saas-envoy-tutorial/IC776778.png "Habilitar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Envoy**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Envoy**, digite a URL usando o padrão "*https://\<nome-locatário>.Envoy.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Envoy**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Envoy.cer**.

    ![Configurar logon único](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurar logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do Envoy como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Configurações**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Clique em **Empresa**.

    ![Empresa](./media/active-directory-saas-envoy-tutorial/IC776783.png "Empresa")

8.  Clique em **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Na seção de configuração da **Autenticação SAML**, realize as seguintes etapas:

    ![Autenticação de SAML](./media/active-directory-saas-envoy-tutorial/IC776785.png "Autenticação de SAML")

    >[AZURE.NOTE] O valor para a ID de Local de Matriz é automaticamente gerada pelo aplicativo.

    1.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital**.

        >[AZURE.TIP] Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Envoy**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL do SAML de HTTP do Provedor de Identidade**.
    3.  Clique em **Salvar alterações**.

10. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar logon único](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há qualquer item de ação para a configuração do provisionamento de usuário para o Envoy. Quando um usuário atribuído tenta fazer logon no Envoy usando o painel de acesso, o Envoy verifica se o usuário existe. Se ainda não houver uma conta de usuário, ela será criada automaticamente pelo Envoy.
##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Envoy, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Envoy**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-envoy-tutorial/IC776787.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-envoy-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->