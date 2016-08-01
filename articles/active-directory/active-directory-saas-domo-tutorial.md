<properties
	pageTitle="Tutorial: Integração do Azure Active Directory ao Domo | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Domo."
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
	ms.date="07/15/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory ao Domo

O objetivo deste tutorial é mostrar como integrar o Domo ao Azure AD (Azure Active Directory). A integração do Domo ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Domo
- Você pode permitir que usuários façam logon automaticamente no Domo (Logon Único) com as respectivas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).


## Pré-requisitos

Para configurar a integração do Azure AD ao Domo, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Domo


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Domo a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## Adição do Domo a partir da galeria
Para configurar a integração do Domo ao Azure AD, você precisará adicionar o Domo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Domo da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Domo**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/tutorial_domo_01.png)

7. No painel de resultados, selecione **Domo** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/tutorial_domo_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Domo, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Domo é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Domo. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor do **Nome de Usuário** no Domo.

Para configurar e testar o logon único do Azure AD com o Domo, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Domo](#creating-a-domo-test-user)** - para ter um equivalente de Brenda Fernandes no Domo que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Domo.

O aplicativo Domo espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_domo_06.png)

**Para configurar o logon único do Azure AD com o Domo, execute as seguintes etapas:**


1. No Portal clássico do Azure, na página de integração do aplicativo **Domo**, no menu superior, clique em **Atributos**.

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_general_80.png)


1. Na caixa de diálogo **Atributos de token SAML**, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:

	| Nome do atributo | Valor do atributo |
	| --- | --- |    
	| name | user.displayname |
	| email | user.mail |

	a. Clique em **adicionar atributo de usuário** para abrir o diálogo **Adicionar Atributo de Usuário**.

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_general_81.png)

	b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.

    d. Clique em **Concluído**.

2. No portal clássico do Azure, na página de integração do aplicativo **Domo**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

3. Na página **Como você deseja que os usuários façam logon no Domo**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_domo_03.png)

4. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_domo_04.png)


    a. Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Domo usando o seguinte padrão: `https://<company name>.domo.com`

    b. Clique em **Próximo**.

5. Na página **Configurar logon único no Domo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_domo_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


6. Para configurar o SSO para o seu aplicativo, entre em contato com a equipe de suporte do Domo pelo email [support@domo.com](mailto: support@domo.com), anexe o certificado baixado e forneça a eles a **URL do Emissor**, a **URL de SSO SAML** e a **URL de Saída**.


7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

8. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_07.png)

8. Na página de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-domo-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criação de um usuário de teste do Domo

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Domo. O Domo dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Domo, caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Domo.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Domo.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Domo, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Domo**.

	![Configurar o logon único](./media/active-directory-saas-domo-tutorial/tutorial_domo_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clicar no bloco Domo no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo Domo.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-domo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-domo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-domo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-domo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-domo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-domo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-domo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-domo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-domo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-domo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-domo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-domo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-domo-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->