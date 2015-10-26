<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Zendesk | Microsoft Azure" description="Saiba como usar o Zendesk com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Zendesk
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522569).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Zendesk. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Zendesk
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Zendesk poderão fazer um logon único no aplicativo do site da sua empresa Zendesk (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração do aplicativo com o Zendesk
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Cenário")

##Habilitando a integração do aplicativo com o Zendesk
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Zendesk.

###Para habilitar a integração de aplicativos com o Zendesk, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Zendesk**.

    ![Galeria de Aplicativos](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Zendesk** e clique em **Concluir** para adicionar o aplicativo.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zendesk com a própria conta no AD do Azure usando federação baseada no protocolo SAML. A configuração do logon único para Zendesk exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Zendesk **, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Logon único](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Logon único")

2.  Na página **Como você deseja que os usuários façam logon no Zendesk**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar logon único")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de entrada do Zendesk**, digite sua URL usando o seguinte padrão "*https://\<nome do locatário>.zendesk.com*" e, em seguida, clique em **Próximo**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Zendesk**, clique em **Baixar certificado** e, em seguida, salve o arquivo do certificado em seu computador como **c:\\zendesk.cer**.

    ![Configurar logon único](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zendesk como administrador.

6.  Clique em **Admin**.

7.  No painel de navegação esquerdo, clique em **Configurações** e, em seguida, clique em **Segurança**.

    ![Segurança](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Segurança")

8.  Na página **Segurança**, selecione a guia **Administrador e Agentes**.

9.  Selecione **Logon Único (SSO) e SAML** e, em seguida, selecione **SAML**.

10. No portal do AD do Azure, na página **Configurar logon único no Zendesk**, copie o valor da **URL de SAML SSO**, em seguida, cole-o na caixa de texto **URL de SAML SSO**.

11. No portal do AD do Azure, na página **Configurar logon único no Zendesk**, copie o valor da **URL de Logout Remoto**, em seguida, cole-o na caixa de texto **URL de Logout Remoto**.

    ![Logon único](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Logon único")

12. Copie o valor **Impressão digital** do certificado exportado e, em seguida, cole-o na caixa de texto **Impressão digital de certificado**.

	>[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI)

13. Clique em **Salvar**.

14. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no **Zendesk**, eles devem ser provisionados no **Zendesk**. No caso do **Zendesk**, o provisionamento é uma tarefa manual.

###Para provisionar uma conta de usuário no Zendesk, execute as seguintes etapas:

1.  Faça logon no seu locatário do **Zendesk**.

2.  Selecione a guia **Lista de Clientes**.

3.  Selecione a guia **Usuário** e, em seguida, clique em **Adicionar**.

    ![Adicionar usuário](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Adicionar usuário")

4.  Digite o endereço de uma conta existente do AD do Azure que você deseja provisionar e, em seguida, clique em **Salvar**.

    ![Novo usuário](./media/active-directory-saas-zendesk-tutorial/IC773633.png "Novo usuário")

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao Zendesk, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Zendesk **, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->