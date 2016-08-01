<properties
	pageTitle="Tutorial: Integração do Azure Active Directory ao Yardi eLearning | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Yardi eLearning."
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
	ms.date="07/14/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory ao Yardi eLearning

O objetivo desse tutorial é mostrar como integrar o Yardi eLearning ao Azure AD (Azure Active Directory).

A integração do Yardi eLearning ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Yardi eLearning
- Você pode permitir que seus usuários façam logon automaticamente no Yardi eLearning (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Yardi eLearning, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada do Yardi eLearning com logon único


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Yardi eLearning a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## Adição do Yardi eLearning a partir da galeria
Para configurar a integração do Yardi eLearning ao Azure AD, você precisará adicionar o Yardi eLearning da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Yardi eLearning da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Yardi eLearning**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_01.png)

7. No painel de resultados, selecione **Yardi eLearning** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Yardi eLearning com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Yardi eLearning é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Yardi eLearning.

Essa relação de vínculo é estabelecida por meio da atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Yardi eLearning.

Para configurar e testar o logon único do Azure AD o com Yardi eLearning, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste do Yardi eLearning](#creating-a-yardi-elearning-test-user)** - para ter um equivalente de Brenda Fernandes no Yardi eLearning vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Yardi eLearning.



**Para configurar o logon único do Azure AD com o Yardi eLearning, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Yardi eLearning**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Yardi eLearning**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 
	![Configurar o logon único](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
 
	![Configurar o logon único](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_04.png)


    a. Na caixa de texto da URL de Entrada, digite a URL usada pelos usuários para fazer logon no seu aplicativo Yardi eLearning usando o seguinte padrão: **“https://<nome da empresa>.yardielearning.com/login”**.

    b. Clique em **Próximo**.


4. Na página **Configurar logon único no Yardi eLearning**, realize as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador.

    b. Clique em **Próximo**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte do Yardi eLearning usando o endereço [elearning@yardi.com](mailto:elearning@yardi.com) e anexe o arquivo de metadados baixado ao email.


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.



![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_03.png)

4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criação de um usuário de teste do Yardi eLearning

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Yardi eLearning. O Yardi eLearning dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Será criado um novo usuário durante uma tentativa de acessar o Yardi eLearning caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, terá de entrar em contato com a equipe de suporte de Yardi eLearning.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Yardi eLearning.

	![Assign User][200] 

**Para atribuir Brenda Fernandes ao Yardi eLearning, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
 
	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Yardi eLearning**.

	![Configurar o logon único](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Yardi eLearning no Painel de Acesso, deverá fazer logon automaticamente em seu aplicativo.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->