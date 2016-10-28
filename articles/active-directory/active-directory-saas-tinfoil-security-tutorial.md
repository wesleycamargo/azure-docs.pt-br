<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Tinfoil Security | Microsoft Azure"
    description="Saiba como usar o Tinfoil Security com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Tinfoil Security
  
O objetivo deste tutorial é mostrar a integração do Azure com o Tinfoil Security. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Tinfoil Security habilitada para logon único
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Tinfoil Security poderão fazer logon único no aplicativo em seu site de empresa do Tinfoil Security (logon iniciado pelo provedor de identidade) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Tinfoil Security
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurar o logon único")

##Habilitando a integração de aplicativos com o Tinfoil Security
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Tinfoil Security.

###Para habilitar a integração de aplicativos com o Tinfoil Security, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Tinfoil Security**.

    ![Galeria de Aplicativos](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Tinfoil Security** e clique em **Concluir** para adicionar o aplicativo.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Tinfoil Security com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o Tinfoil Security exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No Portal Clássico do Azure, na página de integração de aplicativo do **Tinfoil Security**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Tinfoil Security**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Resposta do Tinfoil Security**, digite a URL do ACS (Serviço do Consumidor de Declaração) do Tinfoil Security (por exemplo: "*https://www.tinfoilsecurity.com/saml/consume*") e clique em **Avançar**.

    >[AZURE.NOTE] Você poderá obter a URL do ACS dos Metadados do Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Tinfoil Security**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Tinfoil Security.cer**.

    ![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Tinfoil Security como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Minha Conta**.

    ![Painel](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Painel")

7.  Clique em **Segurança**.

    ![Segurança](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Segurança")

8.  Na página de configuração **Logon Único**, realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Logon Único")

    1.  Selecione **Habilitar SAML**.
    2.  Clique em **Configuração Manual**.
    3.  No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Tinfoil Security**, copie o valor de **URL de SSO do SAML** e cole-o na caixa de texto **URL da Postagem do SAML**.
    4.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado do SAML**.

        >[AZURE.TIP] Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Copie a **ID da sua Conta**.
    6.  Clique em **Salvar**.

9.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurar o logon único")

10. Na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Atributos")

11. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Atributos")

    1.  Clique em **adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite **accountid**.
    3.  Na caixa de texto **Valor do atributo**, copie o valor da ID da conta que você tiver copiado na seção anterior.
    4.  Clique em **Concluído**.

12. Clique em **Aplicar alterações**.

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Tinfoil Security, eles devem ser provisionados no Tinfoil Security. No caso do Tinfoil Security, o provisionamento é uma tarefa manual.

###Para provisionar um usuário, execute as seguintes etapas:

1.  Se o usuário fizer parte de uma conta Enterprise, você precisa entrar em contato com a equipe de suporte do Tinfoil Security para que a conta de usuário seja criada.

2.  Se o usuário for um usuário de SaaS do Tinfoil Security regular, ele pode adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite para o email especificado para criar uma nova conta de usuário do Tinfoil Security.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do Tinfoil Security ou APIs fornecidas pelo Tinfoil Security para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Tinfoil Security, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Tinfoil Security**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->