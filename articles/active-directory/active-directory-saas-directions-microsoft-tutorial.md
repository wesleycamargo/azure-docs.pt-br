<properties pageTitle="Tutorial: integração do Active Directory do Azure ao Directions on Microsoft | Microsoft Azure" description="Saiba como usar o Directions on Microsoft o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao Directions on Microsoft
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=533728).

O objetivo deste tutorial é mostrar a integração do Active Directory do Azure ao Directions on Microsoft. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Directions on Microsoft

Se você ainda não tiver uma assinatura federada do Directions on Microsoft, envie um solicitação por email para "*service@DirectionsOnMicrosoft.com*”.

Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu ao Directions on Microsoft poderão fazer um logon único no aplicativo usando o logon único.

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Directions on Microsoft
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Cenário")
##Habilitando a integração de aplicativos para o Directions on Microsoft

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Directions on Microsoft.

###Para habilitar a integração de aplicativos para o Directions on Microsoft, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Directions on Microsoft**.

    ![Galeria de Aplicativos](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Directions on Microsoft** e clique em **Concluir** para adicionar o aplicativo.

    ![Cenário](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Cenário")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Directions on Microsoft com sua própria conta do Azure AD usando a federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **Directions on Microsoft**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Habilitar Logon Único](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Habilitar Logon Único")

2.  Na página **Como você deseja que os usuários façam logon no Directions on Microsoft**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto URL de Logon, digite ****https://www.directionsonmicrosoft.com/user/login** e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Directions on Microsoft**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar o logon único")

5.  Envie o arquivo de metadados à equipe de suporte do Directions on Microsoft (**service@DirectionsOnMicrosoft.com*). Para permitir que a equipe de suporte do Directions on Microsoft localize sua associação federado no site, inclua informações da sua empresa em seu email.

    >[AZURE.NOTE]O Logon único para o Directions on Microsoft precisa ser habilitado pela equipe de suporte do Directions on Microsoft. Você receberá uma notificação quando o logon único for habilitado.

6.  No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Não há qualquer item de ação para a configuração do provisionamento de usuário para o Directions on Microsoft. Quando um usuário atribuído tenta fazer logon no Directions on Microsoft usando o painel de acesso, o Directions on Microsoft verifica se o usuário existe. Se ainda não houver uma conta de usuário disponível, ela será automaticamente criada pelo Directions on Microsoft.
##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Directions on Microsoft, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Directions on Microsoft**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sim")

<!---HONumber=August15_HO7-->