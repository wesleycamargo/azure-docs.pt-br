<properties pageTitle="Tutorial: integração do Active Directory do Azure ao Cornerstone OnDemand | Microsoft Azure" description="Saiba como usar o Cornerstone OnDemand com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao Cornerstone OnDemand
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=526246).

O objetivo deste tutorial é mostrar a integração do Azure ao Cornerstone OnDemand. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Cornerstone OnDemand

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Cornerstone OnDemand poderão fazer logon único no aplicativo, em seu site de empresa do Cornerstone OnDemand (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Cornerstone OnDemand
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Cenário")
##Habilitando a integração de aplicativos para o Cornerstone OnDemand

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cornerstone OnDemand.

###Para habilitar a integração de aplicativos para o Cornerstone OnDemand, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Cornerstone OnDemand**.

    ![Galeria de Aplicativos](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Cornerstone OnDemand** e clique em **Concluir** para adicionar o aplicativo.

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Cornerstone OnDemand com a própria conta no Azure AD usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **Cornerstone OnDemand**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.

    ![Habilitar Logon Único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Habilitar Logon Único")

2.  Na página **Como você deseja que os usuários façam logon no Cornerstone OnDemand**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do Cornerstone OnDemand**, digite a URL usando o seguinte padrão “**http://company.csod.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Cornerstone OnDemand**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar o logon único")

5.  Envie os itens a seguir para a equipe de suporte do Cornerstone OnDemand:

    1.  O certificado baixado
    2.  O valor da **URL de Logon Remoto**
    3.  O valor da **URL de Logon Remoto**.

    >[AZURE.NOTE]O Logon Único precisa ser configurado pela equipe de suporte do Cornerstone OnDemand. Assim que a configuração for concluída, você receberá uma notificação da equipe de suporte.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Cornerstone OnDemand, eles devem ser provisionados no Cornerstone OnDemand. No caso do Cornerstone OnDemand, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Envie as informações (por exemplo: Nome, Email) sobre o usuário do Azure AD que você deseja provisionar à equipe de suporte do Cornerstone OnDemand.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Cornerstone OnDemand ou APIs fornecidas pelo Cornerstone OnDemand para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Cornerstone OnDemand, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Cornerstone OnDemand**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Atribuir Usuários](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Atribuir Usuários")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->