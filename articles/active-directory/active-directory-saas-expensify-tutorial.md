<properties
	pageTitle="Tutorial: integração do Azure Active Directory com o Expensify | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Expensify."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory com o Expensify

Neste tutorial, você aprenderá como integrar o Expensify com o Azure Active Directory (Azure AD).

A integração do Expensify com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Expensify
- Você pode permitir que seus usuários façam logon automaticamente no Expensify (Logon Único) com as contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD com o Expensify, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Expensify


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Expensify da galeria
2. configurar e testar o logon único do AD do Azure


## Adicionando o Expensify da galeria
Para configurar a integração do Expensify ao Azure AD, você precisa adicionar o Expensify da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Expensify da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Expensify**.
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_01.png)

7. No painel de resultados, selecione **Expensify** e clique em **Concluir** para adicionar o aplicativo.



##  configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Expensify, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Expensify é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Expensify. Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Expensify.

Para configurar e testar o logon único do Azure AD com o Expensify, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usem esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste do Expensify](#creating-an-expensify-test-user)** – para ter um equivalente de Brenda Fernandes no Expensify que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Expensify.


**Para configurar o logon único do Azure AD com o Expensify, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Expensify**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Expensify**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_04.png)


    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo Expensify usando o seguinte padrão: **“https://www.expensify.com/authentication/saml/login”**.


4. Na página **Configurar logon único no Expensify**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador.

    b. Clique em **Próximo**.


5. Para habilitar o SSO no Expensify, primeiro você precisará habilitar o **Controle de Domínio** no aplicativo. Você pode habilitar o Controle de Domínio no aplicativo. As etapas são listadas [aqui](http://help.expensify.com/domain-control). Para obter suporte adicional, você poderá contatar o suporte Expensify via [help@expensify.com](mailto:help@expensify.com). Depois que o Controle de Domínio estiver habilitado, siga estas etapas:
 
	![Configurar o logon único](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png)

	a. Faça logon no seu aplicativo Expensify.
   
	b. Na barra de ferramentas na parte superior, clique em **Administrador**.

	c. No painel esquerdo, clique em **Controle de Domínio**.

	d. Clique no nome do domínio verificado.

	e. No painel esquerdo, clique em **SAML** e selecione **Habilitado**.

	f. Abra os Metadados de Federação baixados do Azure AD, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade**.

  
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
  
	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-expensify-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste do Expensify

Nesta seção, você criará um usuário chamado Brenda Fernandes no Expensify. Trabalhe com a equipe de suporte do Expensify para adicionar os usuários na plataforma do Expensify.

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Expensify.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Expensify.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Expensify, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Expensify**.

	![Configurar o logon único](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco do Expensify no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Expensify.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->