<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Onit | Microsoft Azure" 
    description="Saiba como usar o Onit com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Onit
  
O objetivo deste tutorial é mostrar a integração do Azure ao Onit. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Onit
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Onit poderão fazer logon único no aplicativo em seu site de empresa do Onit (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Onit
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-onit-tutorial/IC791166.png "Cenário")
##Habilitando a integração de aplicativos para o Onit
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Onit.

###Para habilitar a integração de aplicativos para o Onit, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-onit-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-onit-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-onit-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Onit**.

    ![Galeria de Aplicativos](./media/active-directory-saas-onit-tutorial/IC791167.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Onit** e clique em **Concluir** para adicionar o aplicativo.

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Onit com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o Onit exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).
  
Seu aplicativo Onit espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso.

![Logon Único](./media/active-directory-saas-onit-tutorial/IC791168.png "Logon Único")

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Onit**, no menu superior, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-onit-tutorial/IC791169.png "Atributos")

2.  Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    
    |Nome do atributo|Valor do atributo|
	|---|---|
	|name|User.userprincipalname|
    |email|User.mail|

    1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.
    3.  Na lista **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.
    4.  Clique em **Concluído**.

3.  Clique em **Aplicar Alterações**.

4.  Em seu navegador, clique em **Voltar** para abrir a caixa de diálogo o**Início rápido** novamente.

5.  Clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-onit-tutorial/IC791170.png "Configurar o logon único")

6.  Na página **Como você deseja que os usuários façam logon no Onit**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-onit-tutorial/IC791171.png "Configurar o logon único")

7.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Onit**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Onit (por exemplo: "*https://ms-sso-test.onit.com*”) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-onit-tutorial/IC791172.png "Configurar a URL do Aplicativo")

8.  Na página **Configurar logon único no Onit**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-onit-tutorial/IC791173.png "Configurar o logon único")

9.  Em outra janela do navegador da Web, faça logon em seu site de empresa Onit como um administrador.

10. No menu na parte superior, clique em **Administração**.

    ![Administração](./media/active-directory-saas-onit-tutorial/IC791174.png "Administração")

11. Clique em **Editar Empresa**.

    ![Editar Corporação](./media/active-directory-saas-onit-tutorial/IC791175.png "Editar Corporação")

12. Clique na guia **Segurança**.

    ![Editar Informações da Empresa](./media/active-directory-saas-onit-tutorial/IC791176.png "Editar Informações da Empresa")

13. Na guia **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-onit-tutorial/IC791177.png "Logon Único")

    1.  Como **Estratégia de Autenticação**, selecione **Logn Único e Senha**.
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Onit**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Destino do Idp**.
    3.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Onit**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logoff do Idp**.
    4.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado de IdP (SHA1)**.

        >[AZURE.TIP] Para obter mais detalhes, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Para **Tipo de SSO**, selecione **SAML**.
    6.  Na caixa de texto **Texto do botão de logon SSO**, digite o texto de botão desejado.
    7.  Selecione **Logon com SSO: necessário para os seguintes domínios/usuários**, digite o endereço de email de um usuário de teste na caixa de texto correspondente e clique em **Atualizar**. ![Editar Corporação](./media/active-directory-saas-onit-tutorial/IC791178.png "Editar Corporação")

14. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-onit-tutorial/IC791179.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Onit, eles devem ser provisionados no Onit. No caso do Onit, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Onit** como administrador.

2.  Clique em **Adicionar Usuário**.

    ![Administração](./media/active-directory-saas-onit-tutorial/IC791180.png "Administração")

3.  Na página do diálogo **Adicionar usuário**, realize as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-onit-tutorial/IC791181.png "Adicionar usuário")

    1.  Digite o **Nome** e **Endereço de Email** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Criar**.

        >[AZURE.NOTE] O titular da conta receberá um email com um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do Onit ou as APIs fornecidas pelo Onit para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Onit, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Onit**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-onit-tutorial/IC791182.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-onit-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->