<properties pageTitle="Tutorial: integração do Active Directory do Azure ao Dropbox for Business | Microsoft Azure" description="Saiba como usar o Dropbox for Business com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao Dropbox for Business
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522415).
  
O objetivo deste tutorial é mostrar a integração do Azure ao Dropbox for Business. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste no Dropbox for Business
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Dropbox for Business poderão fazer logon único no aplicativo em seu site de empresa do Dropbox for Business (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Dropbox for Business
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Cenário")



##Habilitando a integração de aplicativos para Dropbox for Business
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Dropbox for Business.

###Para habilitar a integração de aplicativos com o Dropbox for Business, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Dropbox for Business**.

    ![Galeria de aplicativos](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Dropbox for Business** e clique em **Concluir** para adicionar o aplicativo.

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Dropbox for Business com sua conta do AD do Azure usando federação baseada em protocolo SAML.

Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Dropbox for Business. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Dropbox for Business**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Dropbox for Business**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

     3\.1. Faça logon no seu locatário do Dropbox for Business. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurar logon único")

     3\.2. No painel de navegação à esquerda, clique em **Console do administrador**.<br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurar logon único")

     3\.3. No **Console do administrador**, clique em **Autenticação** no painel de navegação à esquerda. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurar logon único")

     3\.4. Na seção **Logon único**, selecione **Habilitar logon único** e clique em **Mais** para expandir essa seção. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurar logon único")

     3\.5. Copie a URL ao lado de **Os usuários podem entrar digitando seu endereço de email ou podem ir diretamente para**. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurar logon único")

     3\.6. No portal do Azure, na caixa de texto da URL **logon no DropBox for business**, cole a URL. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurar logon único")



4. Na página **Configurar logon único no Dropbox for Business**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado em seu computador. <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurar logon único")


5. Em seu locatário do Dropbox for Business, na seção **logon único** da página de **Autenticação**, execute as seguintes etapas: <br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar logon único")

     5\.1. Clique em **Obrigatório**.

     5\.2. No portal do Azure, na página de diálogo **Configurar logon único no Dropbox for Business**, copie o valor da **URL da página Logon** e, em seguida, cole-o na caixa de texto **URL de logon**.


     5\.3. Crie um arquivo **codificado em base-64** usando o certificado baixado. >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).


     5\.4. Clique em **Escolher certificado** e navegue até seu **arquivo de certificado codificado em base-64**.


     5\.5. Clique em **Salvar alterações** para concluir a configuração no seu locatário do DropBox for Business.


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.<br><br> ![Configurar logon único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurar logon único")





##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Dropbox for Business.


### Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos **Dropbox for Business**, clique em **Configurar o provisionamento de usuários** para abrir a caixa de diálogo **Configurar Provisionamento de Usuários**.

2. Na página Habilitar provisionamento de usuários para o DropBox for Business, clique em Habilitar provisionamento de usuários para abrir a caixa de diálogo Entrar no Dropbox para vincular com o AD do Azure. <br><br> ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Provisionamento do usuário")

3. Na caixa de diálogo **Entrar no Dropbox para vincular com o AD do Azure**, entre no locatário do Dropbox for Business. <br><br> ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Provisionamento do usuário")



4. Clique em **Permitir** para conceder ao AD do Azure acesso para o Dropbox. <br><br> ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Provisionamento do usuário")



5. Para concluir a configuração, clique no botão **Concluir**.<br><br> ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Provisionamento do usuário")




##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Dropbox for Business, execute as etapas a seguir:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Dropbox for Business**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sim")
  


Agora você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o Dropbox for Business.

Como uma primeira etapa de verificação, você pode conferir o status de provisionamento clicando em **Dashboard** (Painel de Controle) no D na página de integração de aplicativos do **Dropbox for Business** no Portal de Gerenciamento do Azure

<br><br> ![Atribuir usuários](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Atribuir usuários")


Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado.

<br><br> ![Atribuir usuários](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Atribuir usuários")


Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=August15_HO8-->