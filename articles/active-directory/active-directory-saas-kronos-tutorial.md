<properties
	pageTitle="Tutorial: integração do Azure Active Directory ao Kronos | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Kronos."
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
	ms.date="06/29/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory com o Kronos

Neste tutorial, você aprenderá a integrar o Kronos ao Azure AD (Azure Active Directory).

A integração do Kronos ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Kronos
- Você pode permitir que usuários façam logon automaticamente no Kronos (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Kronos, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único na **Kronos Workforce Central**


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Kronos da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Kronos da galeria
Para configurar a integração do Kronos ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Kronos da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Kronos**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_01.png)

7. No painel de resultados, selecione **Kronos** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_06.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Kronos, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Kronos é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kronos. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Kronos.

Para configurar e testar o logon único do Azure AD com o Kronos, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste do Kronos](#creating-an-kronos-test-user)**: para ter um equivalente de Brenda Fernandes no Kronos que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo do Kronos.

O aplicativo Kronos espera que as declarações SAML estejam em um formato específico. Trabalhe com a equipe do Kronos primeiro para verificar o identificador de usuário correto que será mapeado para o aplicativo. Também siga as diretrizes da equipe do Kronos sobre o atributo que deseja usar neste mapeamento. A Microsoft recomenda usar o atributo **"NameIdentifier"** como identificador de usuário. Você pode gerenciar o valor desse atributo na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. Aqui podemos mapear a declaração do nameidentifier com o atributo **userprincipalname** junto com a função **ExtractMailPrefix**, que fornece uma ID de usuário exclusiva, que será enviada para o aplicativo Kronos em cada resposta SAML com êxito.

![Configurar o logon único](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_07.png)


**Para configurar o logon único do Azure AD com o Kronos, realize as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Kronos**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	 ![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Kronos**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 	
	![Configurar o logon único](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_03.png)

3. Na página da caixa de diálogo **Definir Configurações do Aplicativo**, realize as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_04.png)


    a. Na caixa de texto IDENTIFICADOR, digite a URL usada pelos usuários para entrar em seu aplicativo do Kronos usando o seguinte padrão: `https://<company name>.kronos.net/`

	b. Em URL de Resposta, digite a URL no seguinte padrão: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`


4. Na página **Configurar logon único no Kronos**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador.

    b. Clique em **Próximo**.


5.  Para configurar o SSO para o seu aplicativo, entre em contato com seu Gerente de Conta do Kronos e ele ajudará com relação ao canal apropriado para configurar o SSO. Observe que você precisa enviar o email e anexar o arquivo de metadados baixado.
  
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
	
	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
  	
	![Logon único do AD do Azure][11]



### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_03.png)

4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página do diálogo **Obter senha temporária**, clique em **Criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_07.png)

8. Na página do diálogo **Obter senha temporária**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kronos-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste do Kronos

Nesta seção, você criará um usuário chamado Brenda Fernandes no Kronos. O aplicativo Kronos precisa que todos os usuários sejam provisionados no aplicativo antes de fazer o Logon Único. Portanto, trabalhe com o associado de atendimento ao cliente do Kronos para provisionar todos esses usuários no aplicativo.


> [AZURE.NOTE] Se você precisar criar um usuário ou um lote de usuários manualmente, entre em contato com a equipe de suporte do Kronos.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Kronos.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Kronos, execute as seguintes etapas:**

1. No portal clássico, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Kronos**.

	![Configurar o logon único](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco Kronos no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Kronos.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0706_2016-->