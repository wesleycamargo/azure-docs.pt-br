<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Picturepark | Microsoft Azure" description="Saiba como usar o Picturepark com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Picturepark
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524466).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Picturepark. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Picturepark
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Picturepark poderão fazer logon único no aplicativo em seu site de empresa Picturepark (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Picturepark
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Cenário")
##Habilitando a integração de aplicativos para Picturepark
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Picturepark.

###Para habilitar a integração de aplicativos com o Picturepark, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Picturepark**.

    ![Galeria de Aplicativos](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Picturepark** e clique em **Concluir** para adicionar o aplicativo.

    ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Picturepark com sua conta do AD do Azure usando federação baseada em protocolo SAML. A configuração do logon único para Picturepark exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Picturepark**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Picturepark**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon do Picturepark**, digite sua URL usando o seguinte padrão, "**http://company.picturepark.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Picturepark**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurar o logon único")

5.  Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Picturepark como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e, em seguida, clique em **Console de Gerenciamento**.

    ![Console de gerenciamento](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console de gerenciamento")

7.  Clique em **Autenticação** e em **Provedores de identidade**.

    ![Autenticação](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticação")

8.  Na seção **Configuração do provedor de identidade**, execute as seguintes etapas:

    ![Configuração do provedor de identidade](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuração do provedor de identidade")

    1.  Clique em **Adicionar**.
    2.  Digite um nome para sua configuração.
    3.  Selecione **Definir como padrão**.
    4.  No portal do Azure, na página de diálogo **Configurar logon único no Picturepark**, copie o valor de **URL de SAML SSO** e, em seguida, cole-a na caixa de texto **URI do Emissor**.
    5.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Emissor Confiável**.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **JoinDefaultUsersGroup**.
    7.  Para definir o atributo **Emailaddress** na caixa de texto **Declaração**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. ![Configuração](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuração")
8.  Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Picturepark, eles devem ser provisionados no Picturepark. No caso do Picturepark, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon no seu locatário do **Picturepark**.

2.  Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e, em seguida, clique em **Usuários**.

    ![Usuários](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Usuários")

3.  Na guia **Visão geral dos usuários**, clique em **Novo**.

    ![Gerenciamento de usuário](./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gerenciamento de usuário")

4.  Na caixa de diálogo **Criar Usuário**, execute as seguintes etapas:

    ![Criar Usuário](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Criar Usuário")

    1.  Digite o **Endereço de Email**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome**, **Empresa**, **País**, **CEP**, **Cidade** de um usuário válido do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
    2.  Selecione um **Idioma**.
    3.  Clique em **Criar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Picturepark ou as APIs fornecidas pelo Picturepark para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Picturepark, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos **Picturepark**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sim")
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->