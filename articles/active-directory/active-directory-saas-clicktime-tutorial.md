<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao ClickTime | Microsoft Azure" 
    description="Saiba como usar o ClickTime com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao ClickTime

Neste tutorial, você aprenderá a integrar o ClickTime ao Azure AD (Azure Active Directory).

A integração do ClickTime ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ClickTime
- É possível permitir que seus usuários façam logon automaticamente no ClickTime (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD com o ClickTime, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do ClickTime habilitada para logon único


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ClickTime da galeria
2. configurar e testar o logon único do AD do Azure

##Adicionando ClickTime da galeria

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ClickTime.

###Para habilitar a integração de aplicativos para o ClickTime, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-clicktime-tutorial/tic700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-clicktime-tutorial/tic749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-clicktime-tutorial/tic749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ClickTime**.

    ![Galeria de aplicativos](./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ClickTime** e clique em **Concluir** para adicionar o aplicativo.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o ClickTime, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ClickTime é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ClickTime.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **nome de usuário** no ClickTime.

Para configurar e testar o logon único do Azure AD com o ClickTime, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do ClickTime](#creating-a-clicktime-test-user)** –para ter um equivalente de Brenda Fernandes no ClickTime que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.


### Configuração do logon único do Azure AD

O objetivo desta seção é descrever como permitir que os usuários autentiquem no ClickTime com a própria conta no Azure AD usando federação baseada no protocolo SAML.


>[AZURE.IMPORTANT] Para poder configurar o logon único em seu locatário do ClickTime, você precisa primeiro contatar o suporte técnico do ClickTime para que esse recurso seja habilitado.

**Para configurar o logon único do Azure AD com o ClickTime, execute as seguintes etapas:**

1.  No portal clássico do Azure, na página de integração do aplicativo **ClickTime**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Habilitar logon único](./media/active-directory-saas-clicktime-tutorial/tic777277.png "Habilitar logon único")

2.  Na página **Como você deseja que os usuários façam logon no ClickTime**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurar o logon único")

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tic777286.png)

    a. Na caixa de texto **Identificador**, digite a URL com o seguinte padrão: **https://app.clicktime.com/sp/**
	
	b. Na caixa de texto **URL de resposta**, digite a URL com o seguinte padrão: **https://app.clicktime.com/Login/**

	c. Clique em **Avançar**

4.  Na página **Configurar logon único no ClickTime**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurar o logon único")

4.  Em outra janela do navegador da Web, faça logon em seu site de empresa do ClickTime como administrador.

5.  Na barra de ferramentas na parte superior, clique em **Preferências** e em **Configurações de Segurança**.

6.  Na seção de configuração de **Preferências de Logon Único**, realize as seguintes etapas:

    ![Configurações de Segurança](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Configurações de Segurança")

    a. Selecione **Permitir** a entrada usando o SSO (Logon Único) com **Azure AD**.
    
    b. No portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no ClickTime**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **Ponto de Extremidade do Provedor de Identidade**.

    c. Abra seu certificado codificado em Base 64 no **Bloco de Notas**, copie o conteúdo e cole-o na caixa de texto **Certificado X.509**.
    
    d. Clique em **Salvar**.

7.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurar o logon único")

##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no ClickTime, eles devem ser provisionados no ClickTime. No caso do ClickTime, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **ClickTime**.

2.  Na barra de ferramentas na parte superior, clique na **Empresa** e em **Pessoas**.

    ![Pessoas](./media/active-directory-saas-clicktime-tutorial/tic777282.png "Pessoas")

3.  Clique em **Adicionar Pessoa**.

    ![Adicionar Pessoa](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Adicionar Pessoa")

4.  Na seção Nova Pessoa, execute as etapas a seguir:

    ![Pessoas](./media/active-directory-saas-clicktime-tutorial/tic777284.png "Pessoas")

    a. Na caixa de texto **endereço de email**, digite o endereço de email de sua conta do Azure AD.
    
    b. Na caixa de texto **nome completo**, digite o nome de sua conta do Azure AD.

    >[AZURE.NOTE] Se quiser, defina propriedades adicionais do novo objeto pessoa.

    c. Clique em **Salvar**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do ClickTime ou as APIs fornecidas pelo ClickTime para provisionar as contas de usuário do Azure AD.

### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao ClickTime.

![Atribuir usuário][200]

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir Brenda Fernandes ao ClickTime, execute as seguintes etapas**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **ClickTime**.

	![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png)

3. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]

## Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ClickTime no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo do ClickTime.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->