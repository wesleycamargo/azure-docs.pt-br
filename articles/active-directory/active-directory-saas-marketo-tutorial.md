---
title: "Tutorial: Integração do Azure Active Directory ao Marketo | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Marketo."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 20f3a8b006e45e3a94e95b516bca292a82c5fd03


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Azure Active Directory com o Marketo
Neste tutorial, você aprenderá a integrar o Marketo ao Azure AD (Azure Active Directory).

A integração do Marketo ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Marketo
* Você pode permitir que usuários façam logon automaticamente no Marketo (Logon Único) com as respectivas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Marketo, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Marketo

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Marketo da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-marketo-from-the-gallery"></a>Adicionar o Marketo da galeria
Para configurar a integração do Marketo ao Azure AD, você precisará adicionar o Marketo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Marketo da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Marketo**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. No painel de resultados, selecione **Marketo** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Marketo, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Marketo é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Marketo.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Marketo.

Para configurar e testar o logon único do Azure AD com o Marketo, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Marketo](#creating-a-predictix-price-reporting-test-user)** - para ter um equivalente de Brenda Fernandes no Marketo que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Marketo.

**Para configurar o logon único do Azure AD com o Marketo, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Marketo**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Marketo**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: `https://saml.marketo.com/sp`
   
    b. Na caixa de texto **URL de resposta**, digite a URL com o seguinte padrão: `https://login.marketo.com/saml/assertion/\<munchkinid\>`
   
    c. Clique em **Avançar**
4. Na página **Configurar logon único no Marketo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
5. Para obter a Id do Munchkin do aplicativo, faça logon no Marketo usando credenciais de administrador e execute as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão Administração no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique no link do Munchkin
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie a identificação do Munchkin mostrada na tela e conclua sua URL de resposta no assistente de configuração do Azure AD.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. Para configurar o SSO no aplicativo, siga estas etapas:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão Administração no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu Integração e clique em Logon Único
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar as configurações de SAML, clique no botão Editar
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Habilitar** configurações de logon único
   
    f. Insira a ID do emissor, que você copiou do assistente de configuração do Azure AD.
   
    g. Na caixa de texto ID da Entidade, insira a URL como **http://saml.marketo.com/sp**
   
    h. Selecione o local da ID de usuário como **elemento do Identificador de Nome**
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o identificador de usuário não for um valor de UPN, altere o valor na guia Atributo.
    > 
    > 
   
    i. Carregue o certificado que você baixou do assistente de configuração do Azure AD. Salve as configurações.
   
    j. Editar as configurações de redirecionamento de páginas
   
    k. Copie a URL de logon do assistente de configuração do Azure AD na caixa de texto **URL de logon** .
   
    l. Copie a URL de Logout do assistente de configuração do Azure AD na caixa de texto **URL de Logout** .
   
    m. Na URL de erro, copie a URL da instância do Marketo e clique no botão Salvar para salvar as configurações.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Para habilitar o SSO para usuários, conclua as seguintes ações:
   
    a. Faça logon usando credenciais de administrador de aplicativo do Marketo.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue até o menu **Segurança** e clique em **Configurações de Logon** 
   
    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Marque a opção **Exigir SSO** e salve as configurações.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
9. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-marketo-test-user"></a>Criar um usuário de teste do Marketo
Nesta seção, você criará uma usuária chamada Brenda Fernandes no Marketo. Siga estas etapas para criar um usuário na plataforma do Marketo.

1. Faça logon usando credenciais de administrador de aplicativo do Marketo.
2. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. Navegue até o menu **Segurança** e clique em **Usuários e Funções**
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. Clique no link **Convidar Novo Usuário** na guia Usuários
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. No assistente para Convidar Novo Usuário, preencha as informações a seguir
   
    a. Insira o endereço de **Email** do usuário na caixa de texto
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Insira o **Nome** na caixa de texto
   
    c. Insira o **Sobrenome** na caixa de texto
   
    d. Clique em Avançar
6. Na guia **Permissões** , selecione as funções de usuário e clique em Avançar
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Clique no botão Enviar para enviar o convite de usuário
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. Os usuário receberão a notificação de email e precisarão clicar no link e alterar a senha para ativar a conta. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Marketo.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Marketo, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Marketo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Marketo no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo do Marketo.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


