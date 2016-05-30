<properties
	pageTitle="Tutorial: integração do Azure Active Directory ao Skydesk Email | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Skydesk Email."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/22/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory ao Skydesk Email

O objetivo desse tutorial é mostrar como integrar o Skydesk Email ao Azure AD (Azure Active Directory).

A integração do Skydesk Email ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Skydesk Email
- Você pode habilitar os usuários a entrar automaticamente no Skydesk Email (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure Active

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Skydesk Email, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Skydesk Email


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Skydesk Email a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Skydesk Email a partir da galeria
Para configurar a integração do Skydesk Email ao Azure AD, você precisará adicionar o Skydesk Email da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Skydesk Email a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Skydesk Email**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)

7. No painel de resultados, selecione **Skydesk Email** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Skydesk Email baseado em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Skydesk Email é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Skydesk Email.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Skydesk Email.

Para configurar e testar o logon único do Azure AD com o Skydesk Email, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Skydesk Email](#creating-a-Skydesk-Email-test-user)** - para ter um equivalente de Brenda Fernandes no Skydesk Email que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Skydesk Email.



**Para configurar o logon único do Azure AD com o Skydesk Email, realize as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Skydesk Email**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Skydesk Email**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png)


3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
 
	![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png)


    a. Na caixa de texto da URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo Skydesk Email usando o seguinte padrão: **“https://mail.skydesk.jp/portal/<nome da empresa>”**.

    b. Clique em **Próximo**.


4. Na página **Configurar logon único no Skydesk Email**, realize as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para habilitar o SSO no **Skydesk Email**, execute as seguintes etapas:
 
    a. Entre na sua conta do Skydesk Email como administrador.

    b. No menu na parte superior, clique em Configuração e selecione a organização.

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)

    c. Clique em Domínios no painel esquerdo.

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Clique em Adicionar Domínio.

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Insira seu nome de Domínio e verifique o Domínio.

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Clique em **Autenticação SAML** no painel esquerdo

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

6. Na página de diálogo **Autenticação SAML**, realize as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)

	> [AZURE.NOTE] Para usar a autenticação baseada em SAML, primeiro, você deve configurar um **domínio verificado** ou uma **URL de portal**. Você pode definir a URL de portal com o nome exclusivo.

	![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)


    a. No portal clássico do Azure AD, copie o valor de **URL de SSO SAML** e cole-o na caixa de texto **URL de Logon**.

    b. No portal clássico do Azure AD, copie o valor de **URL do Serviço de Saída Única** e cole-o na caixa de texto **URL de Logoff**.

    c. A **URL de Alteração de Senha** é opcional. Deixe em branco.

    d. Clique em **Obter Chave de Arquivo** para escolher o certificado Skydesk Email baixado e clique em **Abrir** para carregar o certificado.

    e. Para **Algoritmo**, selecione **RSA**.

    f. Clique em **Ok** para salvar as alterações.


7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

8. Na página **Confirmação de logon único**, clique em **Concluir**.
  
	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste do Skydesk Email

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Skydesk Email.

a. Clique em **Acesso de Usuário** no painel esquerdo do Skydesk Email e digite seu nome de usuário.

![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)


[AZURE.NOTE] Se você precisar criar usuários em massa, contate a equipe de suporte do Skydesk Email.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Skydesk Email.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Skydesk Email, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
 
	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Skydesk Email**.

	![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Skydesk Email no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Skydesk Email.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->