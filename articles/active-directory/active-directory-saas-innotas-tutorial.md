<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Innotas | Microsoft Azure" description="Saiba como usar o Innotas com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Innotas
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524479).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Innotas. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Innotas
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Innotas poderão fazer um logon único no aplicativo do site da sua empresa Innotas (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Innotas
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-innotas-tutorial/IC777331.png "Cenário")
##Habilitando a integração de aplicativos para o Innotas
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Innotas.

###Para habilitar a integração de aplicativos com o Innotas, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-innotas-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-innotas-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-innotas-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Innotas**.

    ![Galeria de aplicativos](./media/active-directory-saas-innotas-tutorial/IC777332.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Innotas** e clique em **Concluir** para adicionar o aplicativo.

    ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Innotas com sua conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Innotas**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Innotas**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurar o logon único")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de entrada do Innotas**, digite sua URL usando o seguinte padrão "*https://\<nome do locatário>.Innotas.com*" e, em seguida, clique em **Próximo**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Innotas**, baixe seus metadados, clique em **Baixar metadados** e, em seguida, o arquivo de dados localmente como **c:\\InnotasMetaData.xml**.

    ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurar o logon único")

5.  Encaminhe esse arquivo de metadados à equipe de suporte do Innotas. A equipe de suporte precisa configurar o logon único para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Innotas. Quando um usuário atribuído tenta fazer logon no Innotas usando o painel de acesso, o Innotas verifica se o usuário existe. Se não houver conta de usuário ainda, ela é criada automaticamente pelo Innotas.
##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Innotas, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Innotas**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-innotas-tutorial/IC777339.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-innotas-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->