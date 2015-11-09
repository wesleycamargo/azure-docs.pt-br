<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Cisco Webex | Microsoft Azure" 
    description="Saiba como usar o Cisco Webex com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="MarkusVi"  
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

#Tutorial: integração do Active Directory do Azure ao Cisco Webex

O objetivo deste tutorial é mostrar a integração do Azure ao Cisco Webex. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Cisco Webex

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Cisco Webex poderão fazer logon único no aplicativo em seu site de empresa do Cisco Webex (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Cisco Webex
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Cenário")
##Habilitando a integração de aplicativos para o Cisco Webex

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cisco Webex.

###Para habilitar a integração de aplicativos para o Cisco Webex, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Cisco Webex**.

    ![Galeria de Aplicativos](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Cisco Webex** e clique em **Concluir** para adicionar o aplicativo.

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Cisco Webex com a própria conta do Azure AD usando a federação baseada em protocolo SAML.
Como parte deste procedimento, será necessário criar um certificado codificado em base-64.
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Cisco Webex**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Cisco Webex**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto **URL de Logon**, digite a URL de locatário do Cisco Webex (por exemplo: **http://contoso.webex.com*)).
2.  Na caixa de texto **URL de Resposta do Cisco Webex**, digite a **URL de AssertionConsumerService do Cisco Webex** (por exemplo: **https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*)).

4.  Na página **Configurar logon único no Cisco Webex**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurar logon único")

5.  Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Cisco Webex como administrador.

6.  No menu na parte superior, clique em **Administração de Site**.

    ![Administração do Site](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administração do Site")

7.  Na seção **Gerenciar Site**, clique em **Configuração de SSO**.

    ![Configuração de SSO](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuração de SSO")

8.  Na seção Configuração de SSO da Web Federado, execute as seguintes etapas:

    ![Configuração de SSO Federado](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Configuração de SSO Federado")

    1.  Na lista **Protocolo de Federação**, selecione **SAML 2.0**.
    2.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra seu certificado codificado em base-64 no bloco de notas e copie o conteúdo dele.
    4.  Clique em **Importar Metadados do SAML** e cole o certificado codificado em Base 64.
    5.  No portal do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor para SAML (ID do IdP)**.
    6.  No portal do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Serviço SSO do Cliente**.
    7.  Na lista **Formato de NameID**, selecione **Endereço de Email**.
    8.  Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
    9.  No portal do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do Serviço SSO do Cliente**.
    10. Clique em **Atualizar**.

9.  No portal do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, selecione a confirmação de configuração de logon único e clique em **Concluir**.

    ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurar logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Cisco Webex, eles devem ser provisionados no Cisco Webex. No caso do Cisco Webex, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Cisco Webex**.

2.  Vá para **Gerenciar Usuários > Adicionar Usuário**.

    ![Adicionar usuários](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Adicionar usuários")

3.  Na seção Adicionar Usuário, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Adicionar usuário")

    1.  Para **Tipo de Conta**, selecione **Host**.
    2.  Digite as informações de um usuário existente do Azure AD nas seguintes caixas de texto: **Nome, Sobrenome**, **Nome de usuário**, **Email**, **Senha** e **Confirmar Senha**.
    3.  Clique em **Adicionar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Cisco Webex ou APIs fornecidas pelo Cisco Webex para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Cisco Webex, execute as etapas a seguir:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Cisco Webex**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->