<properties pageTitle="Tutorial: integração do Active Directory do Azure ao EmpCenter | Microsoft Azure" description="Saiba como usar o EmpCenter com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao EmpCenter
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=615281).
  
O objetivo deste tutorial é mostrar a integração do Azure ao EmpCenter. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do EmpCenter
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu ao EmpCenter poderão fazer logon único no aplicativo em seu site de empresa do EmpCenter (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o EmpCenter
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-empcenter-tutorial/IC802916.png "Cenário")
##Habilitando a integração de aplicativos para o EmpCenter
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o EmpCenter.

###Para habilitar a integração de aplicativos para o EmpCenter, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-empcenter-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-empcenter-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-empcenter-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa **, digite **EmpCenter**.

    ![Galeria de Aplicativos](./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **EmpCenter** e clique em **Concluir** para adicionar o aplicativo.

    ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no EmpCenter com a própria conta no Azure AD usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **EmpCenter**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no EmpCenter**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurar o logon único")

3.  Na página **Definir Configurações do Aplicativo**, execute as seguintes etapas:

    ![Definir configurações de aplicativo](./media/active-directory-saas-empcenter-tutorial/IC802921.png "Definir configurações de aplicativo")

    1.  Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo EmpCenter (por exemplo: "**https://partner-authenticati.empcenter.com/workforce/SSO.do*).
    2.  Clique em **Próximo**.

4.  Na página **Configurar logon único no EmpCenter**, para baixar seus metadados, clique em **Baixar metadados** e salve o arquivo de metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurar o logon único")

5.  Envie o arquivo de metadados baixado para a equipe de suporte do EmpCenter.

    >[AZURE.NOTE]A equipe de suporte do EmpCenter precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no EmpCenter, eles deverão ser provisionados no EmpCenter. No caso do EmpCenter, as contas de usuário precisam ser criadas pela equipe de suporte do EmpCenter.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do EmpCenter ou as APIs fornecidas pelo EmpCenter para provisionar as contas de usuário do Active Directory do Azure.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao EmpCenter, execute as etapas a seguir:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **EmpCenter**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-empcenter-tutorial/IC802924.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-empcenter-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->