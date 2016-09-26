<properties
	pageTitle="Tutorial: integração do Azure Active Directory ao SAP Business ByDesign | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business ByDesign."
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
	ms.date="09/09/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory ao SAP Business ByDesign

Neste tutorial, você aprenderá a integrar o SAP Business ByDesign ao Azure AD (Azure Active Directory).

A integração do SAP Business ByDesign ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao SAP Business ByDesign
- Você pode habilitar os usuários a fazer logon automaticamente no SAP Business ByDesign (Logon Único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business ByDesign, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SAP Business ByDesign


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP Business ByDesign da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionar o SAP Business ByDesign da galeria
Para configurar a integração do SAP Business ByDesign ao Azure AD, você precisará adicionar o SAP Business ByDesign da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Business ByDesign da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.


3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **SAP Business ByDesign**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. No painel de resultados, selecione **SAP Business ByDesign** e clique em **Concluir** para adicionar o aplicativo.

	![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Business ByDesign, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP Business ByDesign é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP Business ByDesign.

Essa relação de vínculo é estabelecida com a atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no SAP Business ByDesign.

Para configurar e testar o logon único do Azure AD com o SAP Business ByDesign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do SAP Business ByDesign](#creating-an-sap-business-bydesign-test-user)** - para ter um equivalente de Brenda Fernandes no SAP Business ByDesign que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único no aplicativo do SAP Business ByDesign.

O aplicativo SAP Business ByDesign espera as declarações SAML em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.


**Para configurar o logon único do Azure AD com o SAP Business ByDesign, execute as seguintes etapas:**


1. No portal clássico do Azure, na página de integração de aplicativo do **SAP Business ByDesign**, no menu superior, clique em **Atributos**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png)


2. Na lista de atributos de token SAML de atributos, selecione o atributo nameidentifier e clique em **Editar**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png)

3. Na caixa de diálogo Editar Atributo de Usuário, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png)

	a. Na lista de Valores de Atributo, selecione a função **ExtractMailPrefix()**

	b. Na lista de Email, selecione o atributo de usuário que você deseja usar na implementação. Por exemplo, se você quiser usar EmployeeID como identificador exclusivo de usuário e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione **user.extensionattribute2**.

	c. Clique em **Concluído**.
	

4. No portal clássico, na página de integração de aplicativo do **SAP Business ByDesign**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
	 
	![Configurar o logon único][6]

5. Na página **Como você deseja que os usuários façam logon no SAP Business ByDesign**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png)

6. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png)

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo do SAP Business ByDesign usando o seguinte padrão: `https://<servername>.sapbydesign.com`
	
	b. clique em **Avançar**
 
7. Na página **Configurar logon único no SAP Business ByDesign**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador.

    b. Clique em **Próximo**.


8. Para que o SSO seja configurado para o aplicativo, execute as seguintes etapas:

	a. Faça logon no portal do SAP Business ByDesign com direitos de administrador.

	b. Navegue até **Tarefa Comum de Gerenciamento de Aplicativos e Usuários** e clique na guia **Provedor de Identidade**.

	c. Clique em **Novo Provedor de Identidade** e selecione o arquivo XML de metadados que você baixou do portal clássico do Azure. Com a importação dos metadados, o sistema carrega automaticamente o certificado de assinatura e o certificado de criptografia necessários.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

	d. Para incluir a **URL de Serviço de Consumidor de Declaração** na solicitação SAML, selecione **Incluir URL de Serviço de Consumidor de Declaração**.

	e. Clique em **Ativar Logon Único**.

	f. Salve suas alterações.

	g. Clique na guia **Meu Sistema**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

	h. Copie a **URL de SSO** e cole-a na caixa de texto **URL de Logon Único do Azure AD**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

	i. Especifique se o funcionário pode escolher manualmente entre fazer logon com uma ID de usuário e senha ou SSO, selecionando **Seleção de Provedor de Identidade Manual**.

	j. Na seção **URL de SSO**, especifique a URL que deve ser usada pelo funcionário para fazer logon no sistema. Na lista suspensa URL Enviada ao Funcionário, você pode escolher entre as seguintes opções:
	
	**URL não SSO**
 
	O sistema envia apenas a URL normal do sistema ao funcionário. O funcionário não pode fazer logon usando o SSO e deve usar uma senha ou um certificado em vez disso.

	**URL de SSO**

	O sistema envia apenas a URL de SSO ao funcionário. O funcionário pode fazer logon usando o SSO. A solicitação de autenticação é redirecionada por meio do IdP.

	**Seleção Automática**
 
	Se o SSO não estiver ativo, o sistema enviará a URL normal do sistema ao funcionário. Se o SSO estiver ativo, o sistema verificará se o funcionário tem uma senha. Se uma senha estiver disponível, a URL de SSO e a URL não SSO serão enviadas ao funcionário. No entanto, se o funcionário não tiver uma senha, apenas a URL de SSO será enviada ao funcionário.

	k. Salve suas alterações.

9. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
	
	![Logon único do AD do Azure][10]

10. Na página **Confirmação de logon único**, clique em **Concluir**.
 
	![Logon único do AD do Azure][11]


### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
	
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criação de um usuário de teste do SAP Business ByDesign

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP Business ByDesign. Trabalhe com a equipe de suporte do SAP Business ByDesign para adicionar os usuários à plataforma SAP Business ByDesign.

> [AZURE.NOTE] Verifique se o valor de NameID corresponde ao campo de nome de usuário na plataforma SAP Business ByDesign.

### Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao SAP Business ByDesign.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao SAP Business ByDesign, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SAP Business ByDesign**.

	![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png)

3. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP Business ByDesign no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo SAP Business ByDesign.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->