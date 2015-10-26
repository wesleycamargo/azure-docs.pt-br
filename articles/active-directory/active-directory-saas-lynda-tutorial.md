<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Lynda.com | Microsoft Azure" description="Saiba como usar o Lynda.com com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Lynda.com
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524768).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Lynda.com. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Lynda.com
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Lynda.com poderão fazer um logon único no aplicativo do site da sua empresa Lynda.com (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Lynda.com
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-lynda-tutorial/IC781046.png "Cenário")
##Habilitando a integração de aplicativos para o Lynda.com
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Lynda.com.

###Para habilitar a integração de aplicativos com o Lynda.com, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-lynda-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-lynda-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-lynda-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Lynda.com**.

    ![Galeria de Aplicativos](./media/active-directory-saas-lynda-tutorial/IC777524.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Lynda.com** e clique em **Concluir** para adicionar o aplicativo.

    ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Lynda.com com sua conta do AD do Azure usando federação baseada em protocolo de SAML.

>[AZURE.IMPORTANT]Para poder configurar o logon único em seu locatário Lynda.com, você precisa primeiro contatar o suporte técnico do Lynda.com para que esse recurso seja habilitado.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Lynda.com**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Configurar o logon único](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Lynda.com**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurar logon único")

3.  Na página **Configurar URL do aplicativo**, a caixa de texto **URL de entrada do Lynda.com**, digite a URL de locatário do Lynda.com (p.ex.: **https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*) e clique em **Avançar**:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Lynda.com**, para baixar seus metadados, clique em **Baixar metadados** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar logon único](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurar logon único")

5.  Envie o arquivo de metadados baixado para a equipe de suporte do Lynda.com. A equipe de suporte do Lynda.com faz a configuração de logon único para você.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar logon único](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Lynda.com. Quando um usuário atribuído tenta fazer logon no Lynda.com usando o painel de acesso, o Lynda.com verifica se o usuário existe. Se não houver conta de usuário ainda, ela é criada automaticamente pelo Lynda.com.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Lynda.com ou APIs fornecidas pelo Lynda.com para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Lynda.com, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Lynda.com**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-lynda-tutorial/IC777531.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-lynda-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->