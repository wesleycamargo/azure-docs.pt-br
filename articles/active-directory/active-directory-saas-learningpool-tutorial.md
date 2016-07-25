<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Learningpool | Microsoft Azure" 
    description="Saiba como usar o Learningpool com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure com o Learningpool
  
O objetivo deste tutorial é mostrar a integração do Azure com o Learningpool. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Learningpool com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Learningpool poderão fazer logon único no aplicativo em seu site de empresa do Learningpool (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Learningpool
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Cenário")
##Habilitando a integração de aplicativos com o Learningpool
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Learningpool.

###Para habilitar a integração de aplicativos com o Learningpool, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Learningpool**.

    ![Galeria de Aplicativos](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Learningpool** e clique em **Concluir** para adicionar o aplicativo.

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Learningpool com sua conta do AD do Azure usando federação baseada em protocolo SAML.
  
Seu aplicativo Learningpool espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso:

![Atributos de token SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "Atributos de token SAML")

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Learningpool**, no menu superior, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Atributos")

2.  Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    ###

    |Nome do atributo |Valor do atributo |
	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
	|-------------------------------|--------------------------|  
	 urn: oid:2.5.4.42|User.givenname   
    |urn:oid:0.9.2342.19200300.100.1.3|User.mail
    |urn: oid:2.5.4.4|User.surname

    1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.
    3.  Na lista **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.
    4.  Clique em **Concluído**.

3.  Clique em **Aplicar Alterações**.

4.  Em seu navegador, clique em **Voltar** para abrir a caixa de diálogo o**Início rápido** novamente.

5.  Clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurar o logon único")

6.  Na página **Como você deseja que os usuários façam logon no Learningpool**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurar o logon único")

7.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Learningpool**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Learningpool (por exemplo: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurar a URL do Aplicativo")

8.  Na página **Configurar logon único no Learningpool**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurar o logon único")

9.  Encaminhe esse arquivo de metadados à equipe de suporte do Learningpool.

    >[AZURE.NOTE]O logon único deve ser habilitado pela equipe de suporte do Learningpool.

10. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Learningpool, eles devem ser provisionados no Learningpool.
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Learningpool. Os usuários precisam ser criados por sua equipe de suporte do Learningpool.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação de conta de usuário do Learningpool ou APIs fornecidas pelo Learningpool para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Learningpool, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Learningpool**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->