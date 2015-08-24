<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o ZScaler | Microsoft Azure" description="Saiba como usar o ZScaler com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Zscaler
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=521842).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Zscaler. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no Zscaler
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Zscaler
2.  Configurando o logon único
3.  Definindo as configurações de proxy
4.  Configurando o provisionamento de usuários
5.  Atribuindo usuários

![Cenário](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Cenário")

##Habilitando a integração de aplicativos com o Zscaler
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Zscaler.

###Para habilitar a integração de aplicativos com o Zscaler, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Zscaler**.

    ![Galeria de aplicativos](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Zscaler** e clique em **Concluir** para adicionar o aplicativo.

    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zscaler com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário carregar um certificado para Zscaler.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Zscaler**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.

    ![Habilitar logon único](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Habilitar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Zscaler**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Zscaler**, digite a URL de entrada do Zscaler e clique em **Avançar**:

    >[AZURE.NOTE]Entre em contato com a equipe de suporte do Zscaler se você não souber qual é a sua URL de entrada.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Zscaler**, execute as seguintes etapas.

    ![Configurar logon único](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurar logon único")

    1.  Clique em **Baixar certificado** e salve o certificado como **c:\\Zscaler.cer**.
    2.  Copie a **URL de solicitação de autenticação** em sua área de transferência.

5.  Faça login no seu locatário do Zscaler.

6.  No menu na parte superior, clique em **Administração**.

    ![Administração](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administração")

7.  Em ** Gerenciar Administradores e Funções**, clique em **Gerenciar Usuários e Autenticação**.

    ![Gerenciar Administradores e Funções](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gerenciar Administradores e Funções")

8.  Na seção **Escolher Opções de Autenticação para a sua Organização**, execute as seguintes etapas:

    ![Escolha as Opções de Autenticação](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Escolha as Opções de Autenticação")

    1.  Selecione **Autenticar usando o Logon Único do SAML**.
    2.  Clique em **Configurar Parâmetros de Logon Único do SAML**.

9.  Na página de diálogo **Configurar Parâmetros de Logon Único do SAML**, execute as seguintes etapas e, em seguida, clique em **Concluir**:

    ![Carregar um certificado](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Carregar um certificado")

    1.  Na caixa de texto **URL do Portal do SAML para o qual os usuários são enviados para autenticação **, cole o valor do campo **URL de solicitação de autenticação** no portal do Azure.
    2.  Na caixa de texto **Atributo que contém o Nome de Logon**, digite **NameID**.
    3.  No campo **Carregar Certificado Público do SSL**, carregue o certificado que você baixou do portal do Azure.
    4.  Selecione **Habilitar o Provisionamento Automático do SAML**.

10. Na página de caixa de diálogo **Configurar Autenticação de Usuário**, execute as seguintes etapas:

    ![Editar a Autenticação de Usuário](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Editar a Autenticação de Usuário")

    1.  Clique em **Salvar**.
    2.  Clique em **Ativar agora**.

11. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurar logon único")

##Definindo as configurações de proxy

###Para definir as configurações de proxy no Internet Explorer

1.  Inicie o **Internet Explorer**.

2.  Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.

    ![Opções da Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opções da Internet")

3.  Clique na guia **Conexões**.

    ![Conexões](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Conexões")

4.  Clique em **Configurações de LAN** para abrir a caixa de diálogo **Configurações de LAN**.

5.  Na seção Servidor de proxy, execute as seguintes etapas:

    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Servidor proxy")

    1.  Selecione Usar um servidor de proxy para a LAN.
    2.  Na caixa de texto endereço, digite **gateway.zscalertwo.net**.
    3.  Na caixa de texto Porta, digite **80**.
    4.  Selecione **Ignorar servidor proxy para endereços locais**.
    5.  Clique em **OK** para fechar a caixa de diálogo **Configurações de Rede de Área Local (LAN)**.

6.  Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no ZScaler, eles devem ser provisionados no ZScaler. No caso do ZScaler, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça login no seu locatário do **Zscaler**.

2.  Clique em **Administração**.

    ![Administração](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administração")

3.  Clique em **Gerenciamento de Usuário**.

    ![Gerenciamento de Usuário](./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gerenciamento de Usuário")

4.  Na guia **Usuários**, clique em **Adicionar**.

    ![Adicionar](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Adicionar")

5.  Na seção Adicionar Usuário, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Adicionar usuário")

    1.  Digite **UserID**, **Nome de Exibição do Usuário**, **Senha**, **Confirmar Senha** e, em seguida, selecione **Grupos** e o **Departamento** de uma conta válida do AAD que você deseja provisionar.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do ZScaler ou APIs fornecidas pelo ZScaler para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao ZScaler, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ZScaler**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->