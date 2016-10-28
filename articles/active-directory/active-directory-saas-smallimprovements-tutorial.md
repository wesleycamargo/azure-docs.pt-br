<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Small Improvements | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Small Improvements."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o Small Improvements

O objetivo desse tutorial é mostrar como integrar o Small Improvements ao Azure AD (Azure Active Directory). A integração do Small Improvements ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Small Improvements
- Você pode permitir que os usuários façam logon automaticamente no Small Improvements (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Small Improvements, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Small Improvements


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Small Improvements da galeria
2. Configurar e testar o logon único do AD do Azure


## Adição do Small Improvements da galeria
Para configurar a integração do Small Improvements ao Azure AD, você precisará adicionar o Small Improvements à sua lista de aplicativos de SaaS gerenciados a partir da galeria.

**Para adicionar o Small Improvements da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Small Improvements**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)

7. No painel de resultados, escolha **Small Improvements** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Small Improvements com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Small Improvements é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Small Improvements. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Small Improvements.

Para configurar e testar o logon único do Azure AD com o Small Improvements, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Small Improvements](#creating-a-small-improvements-test-user)**: para ter um equivalente de Brenda Fernandes no Small Improvements que esteja vinculado à representação dela no Azure AD.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Small Improvements.



**Para configurar o logon único do Azure AD com o Small Improvements, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Small Improvements**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Small Improvements**, escolha **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png)

    > [AZURE.NOTE] Entre em contato com a equipe do Small Improvements no endereço [Support@small-improvements.com](mailto:support@small-improvements.com) para configurar um nome de domínio para sua conta. Isso é necessário para que o logon único funcione.


    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Small Improvements. b. Clique em **Próximo**.


4. Na página **Configurar logon único no Small Improvements**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


1. Em outra janela do navegador, entre em seu site de empresa do Small Improvements como administrador.

1. Na página do painel principal, clique no botão **Administração** à esquerda.

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png)

1. Clique no botão **SSO de SAML** na seção **Integrações**.

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png)


1. Na página Instalação do SSO, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)

    a. No portal clássico do Azure, na página do diálogo **Configurar logon único no Small Improvements**, copie o valor da **URL de SSO de SAML** e cole-o na caixa de texto **Ponto de Extremidade do HTTP**.

    b. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado x509**.

    c. Se você quiser disponibilizar a opção de autenticação de formulário SSO e Logon para os usuários, marque a opção **Habilitar o acesso por meio de logon/senha também**.
  
	d. Insira o valor apropriado para o botão Nomear o Login SSO na caixa de texto **Prompt de SAML**.

	e. Clique em **Salvar**.


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Avançar**.

7. Na página do diálogo **Obter senha temporária**, clique em **Criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criar um usuário de teste para Small Improvements

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Small Improvements. Você já habilitou isso em [Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).


**Para criar um usuário chamado Brenda Fernandes no Small Improvements, execute as seguintes etapas:**

1. Entre em seu site de empresa do Small Improvements como administrador.

1. Na Home page, no menu à esquerda, e clique em **Administração**.

1. Clique no botão **Diretório de usuários** na seção Gerenciamento de Usuários.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png)

1. Clique em **Adicionar Membro da Equipe**. ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png)

1. Digite o **Nome**, **Sobrenome** e **Endereço de Email** de um usuário válido que você deseja provisionar nas caixas de texto relacionadas. ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png)

1. Você também pode optar por inserir a mensagem pessoal na caixa **Enviar email de notificação**. Se você não quiser enviar a notificação, desmarque essa caixa de seleção.

1. Clique em **Criar Usuários**.

### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Small Improvements.

	![Assign User][200] 

**Para atribuir Brenda Fernandes ao Small Improvements, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Small Improvements**.

	![Configurar o logon único](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png)


1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco Small Improvements no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Small Improvements.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->