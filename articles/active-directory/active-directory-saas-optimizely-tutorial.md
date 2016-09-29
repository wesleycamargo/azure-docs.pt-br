<properties
	pageTitle="Tutorial: integração do Azure Active Directory ao Optimizely | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Optimizely."
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
	ms.date="09/11/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory ao Optimizely

Neste tutorial, você aprenderá a integrar o Optimizely ao Azure AD (Azure Active Directory).

A integração do Optimizely ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Optimizely
- Você pode permitir que os usuários façam logon automaticamente no Optimizely (Logon Único) com as contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Optimizely, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do **Optimizely** habilitada para logon único


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Optimizely da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Optimizely da galeria
Para configurar a integração do Optimizely ao Azure AD, você precisa adicionar o Optimizely da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Optimizely da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Optimizely**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. No painel de resultados, escolha **Optimizely** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o Optimizely, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Optimizely é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Optimizely. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no Optimizely.

Para configurar e testar o logon único do Azure AD com o Optimizely, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do Optimizely](#creating-an-optimizely-test-user)**: para ter um equivalente de Brenda Fernandes no Optimizely que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Optimizely.

O aplicativo Optimizely espera as asserções de SAML para conter um atributo chamado "email". O valor de "email" deve ser um email reconhecido pelo Optimizely que possa ser autenticado pelo Azure AD. Configure a declaração "email" para esse aplicativo. Você pode gerenciar os valores dos atributos na guia **"Atributos"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.


![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png)


**Para configurar o logon único do Azure AD com o Optimizely, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Optimizely**, no menu superior, clique em **Atributos**.
     
    ![Configurar o logon único][5]

2. No diálogo de atributos de token SAML, adicione o atributo "email".

	a. Clique em **adicionar atributo de usuário** para abrir o diálogo **Adicionar Atributo de Usuário**.
	
	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

	b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo "email".

	c. Na lista **Valor do Atributo**, escolha o valor de atributo "userprincipalname" ou qualquer valor que contenha um email reconhecido pelo Azure AD e pelo Optimizely.

	d. Clique em **Concluído**.
3. No menu na parte superior, clique em **Início Rápido**.

	![Configurar o logon único][6]
4. No portal clássico, na página de integração de aplicativos do **Optimizely**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][7]

5. Na página **Como você deseja que os usuários façam logon no Optimizely**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
 	
	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    a. Na caixa de texto **URL de Entrada**, digite: `https://app.optimizely.net/contoso`

	b. Na caixa de texto **Identificador**, digite: `urn:auth0:optimizely:contoso`

	c. Clique em **Avançar**.


	> [AZURE.NOTE] Os valores **URL de Entrada** e **Identificador** são apenas espaços reservados para os valores reais. Você pode encontrar instruções de como adquirir os valores reais do Optimizely mais adiante neste tutorial.

7. Na página **Configurar logon único no Optimizely**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Copie a **URL de Serviço de Logon Único**.

8. Para que o SSO seja configurado para o aplicativo, contate o Gerente de Contas do Optimizely e forneça as seguintes informações:

    - Seu certificado baixado
    - A URL do Serviço de Logon Único
 
    Em resposta ao seu email, o Optimizely fornece a URL de Entrada (SSO iniciado pelo SP) e os valores do Identificador (ID de Entidade de Provedor de Serviços).

9. Volte para a página de diálogo **Definir Configurações do Aplicativo** e execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

	a. Na caixa de texto **URL de Entrada**, digite a **URL do SSO iniciado pelo SP** fornecida pelo Optimizely.

	b. Na caixa de texto **Identificador**, digite a **ID de Entidade de Provedor de Serviços** fornecida pelo Optimizely.

    c. Clique em **Próximo**.

10. Na página **Configurar logon único no Optimizely**, execute as seguintes etapas:
	
	![Logon único do AD do Azure][10]

    a. Escolha a confirmação da configuração de logon único.

    b. Clique em **Avançar**.

11. Na página **Confirmação de logon único**, clique em **Concluir**.
  	
	![Logon único do AD do Azure][11]

12. Em uma janela diferente do navegador, faça logon no aplicativo Optimizely.
13. Clique no nome da sua conta no canto superior direito e em **Configurações da Conta**.

	![Logon único do AD do Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. Na guia Conta, marque a caixa **Habilitar SSO** em Logon Único na seção **Visão geral**.

	![Logon único do AD do Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes. Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste do Optimizely

Nesta seção, você cria um usuário chamado Brenda Fernandes no Optimizely.

1. Na página inicial, escolha a guia **Colaboradores**
2. Clique em **Novo Colaborador** para adicionar um novo colaborador ao projeto.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Preencha o endereço de email e atribua a ele uma função. Clique em **Convidar**.


	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Ele receberá um convite por email. Usando o endereço de email, ele terá que entrar no Optimizely.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Optimizely.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Optimizely, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Optimizely**.

	![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista Todos os Usuários, escolha **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Optimizely no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo Optimizely.

## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->