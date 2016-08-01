<properties
	pageTitle="Tutorial: Integração do Azure Active Directory com o BenSelect | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o BenSelect."
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


# Tutorial: Integração do Azure Active Directory ao BenSelect

Neste tutorial, você aprenderá a integrar o BenSelect ao Azure AD (Azure Active Directory).

A integração do BenSelect ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao BenSelect
- Você pode permitir que os usuários façam logon automaticamente no BenSelect (Logon Único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao BenSelect, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BenSelect


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar BenSelect da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionar BenSelect da galeria
Para configurar a integração do BenSelect ao Azure AD, você precisará adicionar o BenSelect à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BenSelect da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **BenSelect**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_01.png)

7. No painel de resultados, selecione **BenSelect** e clique em **Concluir** para adicionar o aplicativo.

	![Seleção do aplicativo na galeria](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_001.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o BenSelect, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BenSelect é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no BenSelect.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no BenSelect.

Para configurar e testar o logon único do Azure AD com o BenSelect, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do BenSelect](#creating-a-benselect-test-user)**: para ter um equivalente de Brenda Fernandes no BenSelect que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo BenSelect.

O aplicativo BenSelect espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_06.png)

**Para configurar o logon único do Azure AD com o BenSelect, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **BenSelect**, no menu superior, clique em **Atributos**.

	 ![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_07.png)

2. Na caixa de diálogo **Atributos de token SAML**, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:

	| Nome do atributo | Valor do atributo |
	| --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | extractmailprefix([userprincipalname]) |

	a. Clique em **Adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_08.png)

	b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para a linha.

	c. Na lista **Valor do Atributo**, digite ExtractMailPrefix().

	d. Na lista **Email**, digite User.userprincipalname.
	
	e. Clique em **Concluído**

3. No menu na parte superior, clique em **Início Rápido**.

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_09.png)

4. Na página **Como você deseja que os usuários façam logon no BenSelect**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_03.png)

5. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_04.png)

    a. Na caixa de texto **URL de resposta**, digite a URL no seguinte padrão: `https://www.benselect.com/enroll/login.aspx?Path={<tenant name>}`
	
	b. Clique em **Avançar**
 
6. Na página **Configurar logon único no BenSelect**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


7. Para que o SSO seja configurado para o aplicativo, contate a equipe de suporte do BenSelect pelo email [support@selerix.com](mailto:support@selerix.com) e forneça o seguinte:

	- O certificado baixado
	- A URL de SSO do SAML
	- A URL de Saída
	- O Emissor

	> [AZURE.NOTE] Você precisa mencionar que essa integração requer o algoritmo SHA256 (não há suporte para SHA1) para definir o SSO no servidor apropriado, como app2101 etc.

8. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
	
	![Logon único do AD do Azure][10]

9. Na página **Confirmação de logon único**, clique em **Concluir**.
 
	![Logon único do AD do Azure][11]


### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-benselect-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criar um usuário de teste BenSelect

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no BenSelect. Trabalhe com a equipe de suporte do BenSelect para adicionar usuários na conta do BenSelect.

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do BenSelect pelo endereço <mailto:support@selerix.com>.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao BenSelect.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao BenSelect, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **BenSelect**.

	![Configurar o logon único](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_50.png)

3. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BenSelect no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo BenSelect.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->