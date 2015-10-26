<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao Box | Microsoft Azure" description="Saiba como usar o Box com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />




#Tutorial: Integração do Active Directory do Azure ao Box


>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522410).
  
O objetivo deste tutorial é mostrar a integração do Azure ao Box O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste no Box
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Box poderão fazer logon único no aplicativo em seu site de empresa Box (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Box
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-box-tutorial/IC769537.png "Cenário")



##Habilitando a integração de aplicativos para o Box
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Box.

###Para habilitar a integração de aplicativos para o Box, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-box-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-box-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-box-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Box**.

    ![Galeria de aplicativos](./media/active-directory-saas-box-tutorial/IC701023.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Box** e clique em **Concluir** para adicionar o aplicativo.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Box com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. <br> Como parte desse procedimento, é necessário carregar metadados no Box.com.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Ad do Azure, na página de integração de aplicativos do **Box**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769538.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Box**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769539.png "Configurar logon único")

3.  Na página **Configurar URL do aplicativo**, a caixa de texto **URL de locatário do Box**, digite a URL de locatário do Box (p.ex.: https://<mydomainname>.box.com), e clique em **Avançar**:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-box-tutorial/IC669826.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Box**, para baixar seus metadados, clique em **Baixar metadados** e, em seguida, no o arquivo de dados em seu computador.

    ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC669824.png "Configurar logon único")

5.  Encaminhe esse arquivo de metadados à equipe de suporte do Box. A equipe de suporte precisa configurar o logon único para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769540.png "Configurar logon único")
##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Box.

###Para configurar o logon único, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Box**, clique em **Configurar provisionamento do usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário**. <br><br>![Habilitar o provisionamento automático de usuários.](./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar o provisionamento automático de usuários.")

2. Na página de diálogo **Habilitar o provisionamento do usuário ao Box**, clique em **Habilitar o provisionamento do usuário**. <br><br> ![Habilitar o provisionamento automático de usuários.](./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar o provisionamento automático de usuários.")

3. Na página **Logon para conceder acesso ao Box**, forneça as credenciais necessárias e, em seguida, clique em **Autorizar**. <br><br> ![Habilitar o provisionamento automático de usuários.](./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar o provisionamento automático de usuários.")


4. Clique em **Conceder acesso à caixa** para autorizar essa operação e retornar ao Portal de Gerenciamento do Azure. <br><br> ![Habilitar o provisionamento automático de usuários.](./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar o provisionamento automático de usuários.")

5. Para concluir a configuração, clique no botão Concluir.<br><br> ![Habilitar o provisionamento automático de usuários.](./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar o provisionamento automático de usuários.")



##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Box, execute as seguintes etapas:

1. No portal do AD do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Box**, clique em **Atribuir usuários**.<br><br> ![Atribuir usuários](./media/active-directory-saas-box-tutorial/IC769552.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.<br><br> ![Sim](./media/active-directory-saas-box-tutorial/IC767830.png "Sim")
  

Agora, você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o Box.

Como uma primeira etapa de verificação, você pode conferir o status de provisionamento clicando em Dashboard (Painel de Controle) no D na página de integração de aplicativos do Box no Portal de Gerenciamento do Azure.

<br><br> ![Painel](./media/active-directory-saas-box-tutorial/IC769553.png "Painel")

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado:

<br><br> ![Status da integração](./media/active-directory-saas-box-tutorial/IC769555.png "Status da integração")


No seu locatário do Box, os usuários sincronizados estão listados em **Usuários gerenciados** no **Console do Administrador**.

<br><br> ![Status da integração](./media/active-directory-saas-box-tutorial/IC769556.png "Status da integração")


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->