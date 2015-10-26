<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao Adaptive Suite | Microsoft Azure" description="Saiba como usar o Adaptive Suite com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao Adaptive Suite
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=615749).

O objetivo deste tutorial é mostrar a integração do Azure ao Adaptive Suite. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Adaptive Suite

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Adaptive Suite poderão fazer logon único no aplicativo em seu site de empresa Adaptive Suite (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Adaptive Suite
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Cenário")
##Habilitando a integração de aplicativos para o Adaptive Suite

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Adaptive Suite.

###Para habilitar a integração de aplicativos ao Adaptive Suite, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Adaptive Suite**.

    ![Galeria de Aplicativos](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Adaptive Suite** e clique em **Concluir** para adicionar o aplicativo.

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Adaptive Suite com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o Adaptive Suite exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Adaptive Suite**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Adaptive Suite**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar o logon único")

3.  Na página **Definir Configurações do Aplicativo**, na caixa de texto **URL de Resposta**, digite sua URL usando o seguinte padrão "**https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" e clique em **Avançar**.

    >[AZURE.NOTE]Você pode obter esse valor na página **Configurações de SSO do SAML** do Adaptive Suite.

    ![Definir configurações de aplicativo](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único no Adaptive Suite**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Adaptive Suite como administrador.

6.  Vá para **Administrador**.

    ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

7.  Na seção **Usuários e Funções**, clique em **Gerenciar Configurações de SSO do SAML**.

    ![Gerenciar Configurações de SSO do SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gerenciar Configurações de SSO do SAML")

8.  Na página **Configurações de SSO do SAML**, execute as seguintes etapas:

    ![Configurações de SSO do SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Configurações de SSO do SAML")

    1.  Na caixa de texto **Nome do provedor de identidade**, digite um nome para sua configuração.
    2.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Adaptive Suite**, copie o valor de **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do provedor de identidade**.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Adaptive Suite**, copie o valor de **URL de SSO do SAML** e cole-o na caixa de texto **URL de SSO do provedor de identidade**.
    4.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Adaptive Suite**, copie o valor de **URL de SSO do SAML** e cole-o na caixa de texto **URL de logoff personalizado**.
    5.  Para carregar seu certificado baixado, clique em **Escolher arquivo**.
    6.  Como **ID de usuário do SAML**, selecione **Nome de usuário do Adaptive Insights do Usuário**.
    7.  Como **Local da ID de usuário do SAML**, selecione **ID de usuário em NameID do Assunto**.
    8.  Como **Formato de NameID do SAML**, selecione **Endereço de email**.
    9.  Como **Habilitar SAML**, selecione **Permitir SSO do SAML e direcionar logon do Adaptive Insights**.
    10. Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Adaptive Suite, eles devem ser provisionados no Adaptive Suite. No caso do Adaptive Suite, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **Adaptive Suite** como um administrador.

2.  Vá para **Administrador**.

    ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

3.  Na seção **Usuários e Funções**, clique em **Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Adicionar usuário")

4.  Na seção **Novo Usuário**, execute as seguintes etapas:

    ![Enviar](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Enviar")

    1.  Digite o **Nome**, **Logon**, **Email** e **Senha** de um usuário válido do Active Directory do Azure que deseja provisionar nas caixas de texto relacionadas.
    2.  Selecione uma **Função**.
    3.  Clique em **Enviar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Adaptive Suite ou as APIs fornecidas pelo Adaptive Suite para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Adaptive Suite, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Adaptive Suite**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->