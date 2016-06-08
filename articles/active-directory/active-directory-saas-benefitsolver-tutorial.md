<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Benefitsolver | Microsoft Azure"
    description="Saiba como usar o Benefitsolver com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/26/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure ao Benefitsolver

O objetivo deste tutorial é mostrar a integração do Azure ao Benefitsolver. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Benefitsolver

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Benefitsolver poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Benefitsolver
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Cenário")
##Habilitando a integração de aplicativos para o Benefitsolver

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Benefitsolver.

###Para habilitar a integração de aplicativos para o Benefitsolver, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Benefitsolver**.

    ![Galeria de Aplicativos](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Benefitsolver** e clique em **Concluir** para adicionar o aplicativo.

    ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Benefitsolver com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Seu aplicativo Benefitsolver espera as declarações do SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso.

![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Benefitsolver**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Benefitsolver**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurar o logon único")

3.  Na página **Definir Configurações do Aplicativo**, execute as seguintes etapas:

    ![Definir configurações de aplicativo](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Definir configurações de aplicativo")

    1.  Na caixa de texto **URL de Entrada**, digite **http://azure.benefitsolver.com**.
    2.  Na caixa de texto **URL de Resposta**, digite **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.

    3.  Clique em **Próximo**.

4.  Na página **Configurar logon único no Benefitsolver**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurar o logon único")

5.  Envie o arquivo de metadados baixado para a equipe de suporte do Benefitsolver.

    >[AZURE.NOTE] A equipe de suporte do Benefitsolver precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurar o logon único")

7.  Na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Atributos")

8.  Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    ![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

	|Nome do atributo|Valor do atributo|
    |---|---|
    |ClientID|Você precisa obter esse valor com a equipe de suporte do Benefitsolver.|
    |ClientKey|Você precisa obter esse valor com a equipe de suporte do Benefitsolver.|
    |LogoutURL|Você precisa obter esse valor com a equipe de suporte do Benefitsolver.|
    |EmployeeID|Você precisa obter esse valor com a equipe de suporte do Benefitsolver.|

    1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.
    3.  Na caixa de texto **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.
    4.  Clique em **Concluído**.

9.  Clique em **Aplicar alterações**.

##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Benefitsolver, eles deverão ser provisionados no Benefitsolver. No caso do Benefitsolver, os dados de funcionários são preenchidos em seu aplicativo por meio de um arquivo de censo do sistema HRIS (geralmente à noite).

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do Benefitsolver ou as APIs fornecidas pelo Benefitsolver para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Benefitsolver, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Benefitsolver**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0511_2016-->