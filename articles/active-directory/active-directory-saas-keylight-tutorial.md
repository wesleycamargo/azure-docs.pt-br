<properties
	pageTitle="Tutorial: Integração do Azure Active Directory ao Keylight | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Keylight."
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
	ms.date="07/11/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory ao Keylight

Neste tutorial, você aprenderá como integrar o Keylight ao Azure AD (Azure Active Directory).

A integração do Keylight ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Keylight
- Você pode habilitar os usuários a entrar automaticamente no Keylight (Logon Único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Keylight, você precisará dos seguintes itens:

- Uma assinatura do Azure
- Uma assinatura habilitada para logon único do Keylight


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Keylight a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## Adição do Keylight a partir da galeria
Para configurar a integração do Keylight ao Azure AD, você precisará adicionar o Keylight da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Keylight a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Keylight**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. No painel de resultados, selecione **Keylight** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Keylight, com base em uma usuária de teste chamada "Brenda Fernandes".

Para configurar e testar o logon único do Azure AD com o Keylight, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Keylight](#creating-a-keylight-test-user)**: para ter um equivalente de Brenda Fernandes no Keylight vinculado à representação dela do Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

Nesta seção, você habilita o logon único do Azure AD no portal clássico do Azure e configura o logon único em seu aplicativo Keylight.


**Para configurar o logon único do Azure AD com o Keylight, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Keylight**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]


2. Na página **Como você deseja que os usuários façam logon no Keylight**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
 
	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png)


    a. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para fazer logon no aplicativo Keylight usando o seguinte padrão: **“https://<nome da empresa>.keylightgrc.com/Login.aspx?saml=1”**.


4. Em **Configurar logon único no Keylight**, execute as seguintes etapas:
 
	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para habilitar o SSO no Keylight, execute as seguintes etapas:
 
    a. Faça logon em sua conta do Keylight como administrador.

    b. No menu na parte superior, clique em **Pessoa** e escolha **Instalação do Keylight**.
       
	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/401.png)

    c. No modo de exibição de árvore à esquerda, clique em **SAML**.

	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/402.png)

    d. Na caixa de diálogo **Configurações de SAML**, clique em **Editar**.

	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/404.png)
  

5. Na página de caixa de diálogo **Editar Configurações de SAML**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/405.png)

    a. Defina **Autenticação SAML** como **Ativa**.


    b. No portal clássico do Azure AD, copie o valor de **URL de SSO SAML** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.

    c. No portal clássico do Azure AD, copie o valor de **URL do Serviço de Saída Única** e cole-o na caixa de texto **URL de Logoff do Provedor de Identidade**.

    d. Clique em **Escolher Arquivo** para escolher o certificado do Keylight baixado e clique em **Abrir** para carregar o certificado.


    e. Defina **Local da ID de Usuário do SAML** como **Elemento NameIdentifier da declaração subject**.
   
    f. Forneça o **Provedor de Serviços do Keylight usando o seguinte padrão: **https://&lt;Company Nome&gt;.keylightgrc.com**.

    g. Defina **Provisionar usuários automaticamente** como **Ativo**.

    h. Defina **Provisionar tipo de conta automaticamente** como **Usuário Completo**.

    i. Para **Função de segurança de provisionamento automático**, escolha **Usuário Padrão com SAML**.
   
    j. Para **Configuração de segurança de provisionamento automático**, selecione **Configuração de Usuário Padrão**.
   
    k. Na caixa de texto Atributo de email, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Na caixa de texto **Atributo de nome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Na caixa de texto **Atributo de sobrenome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Clique em **Salvar**.
   
  
   
  
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico do Azure chamada Brenda Fernandes.

Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]



**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png)


2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png)


4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criação de um usuário de teste do Keylight

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Keylight. O Keylight dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Se ainda não existir, um novo usuário será criado quando o Keylight for acessado.

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Keylight.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Keylight.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Keylight, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Keylight**.

	![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Keylight no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Keylight.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->