<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Bonus.ly | Microsoft Azure" 
    description="Saiba como usar o Bonus.ly com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Bonus.ly

O objetivo deste tutorial é mostrar a integração do Azure ao Bonus.ly. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste no Bonus.ly

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Bonus.ly
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-bonus-tutorial/IC773679.png "Cenário")
##Habilitando a integração de aplicativos para o Bonus.ly

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Bonus.ly.

###Para habilitar a integração de aplicativos para o Bonus.ly, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Habilitar logon único](./media/active-directory-saas-bonus-tutorial/IC773680.png "Habilitar logon único")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-bonus-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bonus-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Bonus.ly**.

    ![Galeria de aplicativos](./media/active-directory-saas-bonus-tutorial/IC773681.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Bonus.ly** e clique em **Concluir** para adicionar o aplicativo.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Bonus.ly com a respectiva conta do AD do Azure usando federação baseada em protocolo de SAML. Configurar o logon único para o Bonus.ly exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Bonus.ly**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Bonus.ly**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Locatário do Bonus.ly**, digite a URL usando o padrão "*https://\<nome-locatário>.Bonus.ly*" e clique em **Avançar**:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Bonus.ly**, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Bonusly.cer**.

    ![Configurar logon único](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar o logon único")

5.  Em outra janela do navegador, faça logon no seu locatário do **Bonus.ly**.

6.  Na barra de ferramentas na parte superior, clique em **Configurações** e selecione **Integrações e aplicativos**.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Em **Logon Único**, selecione **SAML**.

8.  Na página do diálogo **SAML**, realize as seguintes etapas:

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Bonus.ly**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de destino de SSO do IdP**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Bonus.ly**, copie o valor da **ID do Emissor** e cole-o na caixa de texto **Emissor do IdP**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Bonus.ly**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IdP**.
    4.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado**.

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  Clique em **Salvar**.

10. No portal do AD do Microsoft Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Bonus.ly, eles devem ser provisionados no Bonus.ly. No caso do Bonus.ly, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Em uma janela do navegador da Web, faça logon no seu locatário do Bonus.ly.

2.  Clique em **Configurações**

    ![Configurações](./media/active-directory-saas-bonus-tutorial/IC781041.png "Configurações")

3.  Clique na guia **Usuários e bônus**.

    ![Usuários e bônus](./media/active-directory-saas-bonus-tutorial/IC781042.png "Usuários e bônus")

4.  Clique em **Gerenciar Usuários**.

    ![Gerenciar Usuários](./media/active-directory-saas-bonus-tutorial/IC781043.png "Gerenciar Usuários")

5.  Clique em **Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-bonus-tutorial/IC781044.png "Adicionar usuário")

6.  No diálogo **Adicionar Usuário**, realize as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-bonus-tutorial/IC781045.png "Adicionar usuário")

    1.  Digite “**Email**, **Nome** e **Sobrenome**” de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE]O titular da conta do AAD receberá um email com um link de confirmação de conta no qual ele deve clicar para torná-la ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Bonus.ly ou as APIs fornecidas pelo Bonus.ly para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Bonus.ly, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do Bonus.ly, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-bonus-tutorial/IC773690.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-bonus-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->