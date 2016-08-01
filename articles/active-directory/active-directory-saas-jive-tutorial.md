<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Jive | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Jive."
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
	ms.date="06/07/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o Jive

Neste tutorial, você aprenderá a integrar o Jive ao Azure AD (Azure Active Directory).

A integração do Jive ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Jive
- Você pode permitir que seus usuários faça logon automaticamente no Jive (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD com o Jive, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Jive


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Fuse da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionar Fuse da galeria
Para configurar a integração do Jive com o Azure AD, você precisará adicionar o Jive à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Jive por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Jive**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. No painel de resultados, selecione **Jive** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Jive, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Jive é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jive.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Jive.

Para configurar e testar o logon único do Azure AD com o Jive, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Jiveuse](#creating-a-jive-test-user)** - para ter um equivalente de Brenda Fernandes no Jive que esteja vinculado à representação dela no Azure AD.
4. **[Configurar o provisionamento de usuários](#configuring-user-provisioning)** - para descrever como habilitar o provisionamento de usuários das contas de usuário do Active Directory para o Jive.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
6. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Jive.

**Para configurar o logon único do Azure AD com o Jive, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Jive**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
	 
	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Jive**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png)

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo do Jive usando o seguinte padrão: **https://\<nome do cliente>.jivecustom.com**.
	
	b. Clique em **Avançar**
 
4. Na página **Configurar logon único no Jive**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Faça logon no seu locatário do Jive como administrador.

6. No menu na parte superior, clique em "**Saml**".

	![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

	a. Selecione **Habilitado** na guia **Geral**.

	b. Clique no botão "**Salvar todas as configurações de saml**".

7. Navegue até a guia "**Metadados Idp**".

	![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

	a. Copie o conteúdo do arquivo XML de metadados baixado e cole-o na caixa de texto **Metadados do IDP (Provedor de Identidade)**.

	b. Clique no botão "**Salvar todas as configurações de saml**".

8. Vá até a guia "**Mapeamento de Atributo de Usuário**".

	![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

	a. Na caixa de texto **Email**, copie e cole o nome de atributo do valor **email**.

	b. Na caixa de texto **Nome**, copie e cole o nome do atributo do valor **nome**.

	c. Na caixa de texto **Sobrenome**, copie e cole o nome do atributo do valor **sobrenome**.
	
9. No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Avançar**. ![Logon único do AD do Azure][10]

10. Na página **Confirmação de logon único**, clique em **Concluir**. ![Logon único do AD do Azure][11]


### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



###Criar um usuário de teste Jive

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Jive. Trabalhe com a equipe de suporte o Jive para adicionar os usuários na plataforma do Jive.


###Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Jive. Como parte desse procedimento, é necessário fornecer um token de segurança do usuário que será necessário solicitar em Jive.com.
  
A captura de tela a seguir mostra um exemplo de caixa de diálogo correspondente no AD do Azure:

![Configure o provisionamento do usuário](./media/active-directory-saas-jive-tutorial/IC698794.png "Configure o provisionamento do usuário")

####Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Jive**, clique em **Configurar provisionamento de usuário** para abrir o diálogo **Configurar Provisionamento de Usuário**.

2.  Na página **Inserir suas credenciais do Jive para habilitar o provisionamento automático de usuário**, forneça as seguintes configurações:

    1.  Na caixa de texto **Nome do Usuário Administrador do Jive**, digite o nome da conta do Jive que tem o perfil do **Administrador de Sistema** atribuído no Jive.com.

    2.  Na caixa de texto **Senha do Administrador do Jive**, digite a senha desta conta.

    3.  Na caixa de texto **URL de Locatário do Jive**, digite a URL de locatário do Jive.

        >[AZURE.NOTE] A URL de locatário do Jive é usada pela sua organização para efetuar logon no Jive. Normalmente, a URL tem o seguinte formato: **www.<organização>.jive.com**.

    4.  Clique em **validar** para verificar sua configuração.

    5.  Clique no botão **Avançar** para abrir a página **Confirmação**.

3.  Na página **Confirmação**, clique na marca de seleção para salvar sua configuração.
  
Agora, você pode criar uma conta de teste, aguardar 10 minutos e verificar se a conta foi sincronizada no Jive.com.




### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Jive.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Jive, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Jive**.

	![Configurar o logon único](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png)

3. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

4. Na lista Usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Jive no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo do Jive.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->