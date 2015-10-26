<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao Cherwell | Microsoft Azure" description="Saiba como usar o Cherwell com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao Cherwell
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=550980).

O objetivo deste tutorial é mostrar a integração do Azure ao Cherwell. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Cherwell

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Cherwell poderão fazer logon único no aplicativo em seu site de empresa Cherwell (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Cherwell
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Cenário")
##Habilitando a integração de aplicativos para o Cherwell

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cherwell.

###Para habilitar a integração de aplicativos para o Cherwell, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Cherwell**.

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  No painel de resultados, selecione **Cherwell** e clique em **Concluir** para adicionar o aplicativo.
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Cherwell com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Cherwell**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Cherwell**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no **Cherwell** (por exemplo: **https://pictdev.cherwellondemand.com/cherwellclient*).
2.  Clique em **Próximo**.

4.  Na página **Configurar logon único no Cherwell**, execute as seguintes etapas.

    ![Configurar o logon único](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar o logon único")

    1.  Clique em **Baixar certificado** e salve o certificado localmente no computador.
    2.  Copie a **URL do Provedor de Identidade**.
    3.  Copie a **URL de Serviço de Logon Único**.
    4.  Clique em **Próximo**.

5.  Envie o certificado de download, a **URL do Provedor de Identidade** e a **URL de Serviço de Logon Único** para a equipe de suporte do Cherwell.

    >[AZURE.NOTE]A equipe de suporte do Cherwell precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Cherwell, eles devem ser provisionados no Cherwell. No caso do Cherwell, as contas de usuário precisam ser criadas pela equipe de suporte do Cherwell.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do Cherwell ou as APIs fornecidas pelo Cherwell para provisionar as contas de usuário do Active Directory do Azure.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Cherwell, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Cherwell**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->