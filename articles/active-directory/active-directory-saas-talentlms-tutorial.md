<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao TalentLMS | Microsoft Azure" 
    description="Saiba como usar o TalentLMS com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao TalentLMS
  
O objetivo deste tutorial é mostrar a integração do Azure ao TalentLMS. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do TalentLMS
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TalentLMS poderão fazer logon único no aplicativo em seu site de empresa do TalentLMS (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o TalentLMS
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Cenário")

##Habilitando a integração de aplicativos para o TalentLMS
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TalentLMS.

###Para habilitar a integração de aplicativos com o TalentLMS, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **TalentLMS**.

    ![Galeria de aplicativos](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **TalentLMS** e clique em **Concluir** para adicionar o aplicativo.

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TalentLMS com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o TalentLMS exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **TalentLMS**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no TalentLMS**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do TalentLMS**, digite a URL usando o padrão "*https://\<nome-locatário>.TalentLMSapp.com*" e clique em **Avançar**.

    ![URL de logon](./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL de logon")

4.  Na página **Configurar logon único no TalentLMS**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\TalentLMS.cer**.

    ![Configurar o logon único](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa TalentLMS como um administrador.

6.  Na seção **Conta e Configurações**, clique na guia **Usuários**.

    ![Conta e Configurações](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Conta e Configurações")

7.  Clique em **SSO (Logon Único)**.

8.  Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Logon Único")

    1.  Na lista **Tipo de integração de SSO**, selecione **SAML 2.0**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no TalentLMS**, copie o valor da **ID de Provedor de Identidade** e cole-o na caixa de texto **IdP (Provedor de Identidade)**.
    3.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado**.

        >[AZURE.TIP]Para obter mais detalhes, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  No portal do Azure, na página do diálogo **Configurar logon único no TalentLMS**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de entrada remota**.
    5.  No portal do Azure, na página do diálogo **Configurar logon único no TalentLMS**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de saída remota**.
    6.  Na caixa de texto **TargetedID**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
7.  Na caixa de texto **Nome**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
8.  Na caixa de texto **Sobrenome**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
9.  Na caixa de texto **Email**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
10. Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no TalentLMS, eles devem ser provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **TalentLMS**.

2.  Clique em **Usuários** e em **Adicionar Usuário**.

3.  Na página do diálogo **Adicionar usuário**, realize as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Adicionar usuário")

    1.  Digite os valores dos atributos relacionados da conta de usuário do Azure AD nas seguintes caixas de texto: **Nome**, **Sobrenome** e **Endereço de email**.
    2.  Clique em **Adicionar Usuário**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do TalentLMS ou as APIs fornecidas pelo TalentLMS para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao TalentLMS, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **TalentLMS**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->