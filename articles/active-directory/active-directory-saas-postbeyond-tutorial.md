<properties
	pageTitle="Tutorial: Integração do Azure Active Directory com o PostBeyond | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o PostBeyond."
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
	ms.date="08/09/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory com o PostBeyond

Neste tutorial, você aprenderá a integrar o PostBeyond ao Azure AD (Azure Active Directory).

A integração do PostBeyond ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao PostBeyond
- Você pode permitir que os usuários façam logon automaticamente no PostBeyond (Logon Único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao PostBeyond, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do **PostBeyond**


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o PostBeyond da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o PostBeyond da galeria
Para configurar a integração do PostBeyond ao Azure AD, você precisa adicionar o PostBeyond por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PostBeyond da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **PostBeyond**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_01.png)

7. No painel de resultados, selecione **PostBeyond** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_02.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o PostBeyond, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PostBeyond é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PostBeyond. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de usuário** no PostBeyond.

Para configurar e testar o logon único do Azure AD com o PostBeyond, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do PostBeyond](#creating-a-PostBeyond-test-user)** - para ter um equivalente de Brenda Fernandes no PostBeyond que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo PostBeyond.


**Para configurar o logon único do Azure AD com o PostBeyond, execute as seguintes etapas:**

1. No menu na parte superior, clique em **Início Rápido**.

	![Configurar o logon único][6]

2. No portal clássico do Azure, na página de integração do aplicativo **PostBeyond**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][7]

3. Na página **Como você deseja que os usuários façam logon no PostBeyond**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 	
	![Configurar o logon único](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_06.png)

4. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_07.png)


    a. Na caixa de texto URL de Entrada, digite uma URL usando o seguinte padrão: `https://app.postbeyond.com`.

	b. Clique em **Avançar**.

5. Na página **Configurar logon único no PostBeyond**, clique em **Baixar certificado** e salve o arquivo no computador. Além disso, copie os valores da URL do emissor, da URL do serviço de logon único e da URL do serviço de logoff único. Você precisará compartilhar essas informações com o suporte do PostBeyond para configurar o SSO.

	![Configurar o logon único](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_08.png)

6. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do PostBeyond pelo telefone <sso@postbeyond.com>. Eles ajudarão com o canal apropriado para configurar o SSO e forneça a eles o seguinte:

	- O certificado baixado
	- A **URL do Emissor**
	- A **URL de SSO do SAML**
	- A **URL do Serviço de Logoff Único**

7. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
	
	![Logon único do AD do Azure][10]

8. Na página **Confirmação de logon único**, clique em **Concluir**.
  	
	![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-postbeyond-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste do PostBeyond

Nesta seção, você criará uma usuária chamada Brenda Fernandes no PostBeyond. Se você não souber como adicionar Brenda Fernandes ao PostBeyond, trabalhe com a equipe de suporte do PostBeyond para adicionar o usuário de teste e habilitar o SSO. Entre em contato com eles pelo email <sso@postbeyond.com>.

### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao PostBeyond.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao PostBeyond, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **PostBeyond**.

	![Configurar o logon único](./media/active-directory-saas-postbeyond-tutorial/tutorial_postbeyond_09.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista Todos os Usuários, escolha **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco PostBeyond no Painel de Acesso, deve acessar a página de entrada do PostBeyond. Clique em **Entrar com o Office 365**, insira suas credenciais do Azure AD. Em seguida, você deve estar conectado ao PostBeyond.

## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-postbeyond-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->