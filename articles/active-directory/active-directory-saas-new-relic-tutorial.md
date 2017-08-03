---
title: "Tutorial: Integração do Azure Active Directory ao New Relic | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o New Relic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 605e85c23a849f70fcc0237361d7a891f716ca3a
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração do Active Directory do Azure com o New Relic

Neste tutorial, você aprenderá a integrar o New Relic ao Azure AD (Azure Active Directory).

A integração do New Relic ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao New Relic
- Você pode permitir que seus usuários façam logon automaticamente no New Relic (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o New Relic, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do New Relic

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o New Relic da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-new-relic-from-the-gallery"></a>Adicionar o New Relic da Galeria
Para configurar a integração do New Relic ao Azure AD, você precisará adicionar o New Relic da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o do New Relic da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **New Relic**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_search.png)

5. No painel de resultados, selecione **New Relic** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o New Relic, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do New Relic é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no New Relic.

No New Relic, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o New Relic, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do New Relic](#creating-a-new-relic-test-user)** – para ter um equivalente de Brenda Fernandes no New Relic que esteja vinculado à representação desse usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo New Relic.

**Para configurar o logon único do Azure AD com o New Relic, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **New Relic**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_samlbase.png)

3. Na seção **URLs e Domínio do New Relic**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.newrelic.com`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do New Relic](https://support.newrelic.com/) para obter o valor. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do New Relic**, clique em **Configurar o New Relic** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_configure.png) 

7. Em outra janela do navegador da Web, entre em seu site de empresa do **New Relic** como administrador.

8. No menu na parte superior, clique em **Configurações de Conta**.
   
    ![Configurações de Conta](./media/active-directory-saas-new-relic-tutorial/ic797036.png "Configurações de Conta")

9. Clique na guia **Segurança e autenticação** e na guia **Logon único**.
   
    ![Logon Único](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Logon Único")

10. Na página da caixa de diálogo SAML, execute as seguintes etapas:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Clique em **Escolher Arquivo** para carregar seu certificado baixado do Active Directory do Azure.

   b. Na caixa de texto **URL de Acesso Remoto**, cole o valor da **URL de Serviço de Logon Único do SAML** copiado do Portal do Azure.
   
   c. Na caixa de texto **URL de chegada de Logoff**, cole o valor da **URL de Saída** copiado do Portal do Azure.

   d. Clique em **Salvar minhas alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-new-relic-test-user"></a>Criar um usuário de teste do New Relic

Para permitir que os usuários do Azure Active Directory façam logon no New Relic, eles deverão ser provisionados no New Relic. No caso do New Relic, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário no New Relic, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **New Relic** como administrador.

2. No menu na parte superior, clique em **Configurações de Conta**.
   
    ![Configurações de Conta](./media/active-directory-saas-new-relic-tutorial/ic797040.png "Configurações de Conta")

3. No painel **Conta** no lado esquerdo, clique em **Resumo** e em **Adicionar usuário**.
   
    ![Configurações de Conta](./media/active-directory-saas-new-relic-tutorial/ic797041.png "Configurações de Conta")

4. No diálogo **Usuários ativos** , realize as seguintes etapas:
   
    ![Usuários ativos](./media/active-directory-saas-new-relic-tutorial/ic797042.png "Usuários ativos")
   
    a. Na caixa de texto **Email** , digite o endereço de email de um usuário válido do Active Directory do Azure que você deseja provisionar.

    b. Como **Função**, selecione **Usuário**.

    c. Clique em **Adicionar este usuário**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do New Relic ou as APIs fornecidas pelo New Relic para provisionar as contas de usuário do AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você concederá a Brenda Fernandes acesso ao New Relic para que ela fique habilitada a usar o logon único do Azure.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao New Relic, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **New Relic**.

    ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/tutorial_newrelic_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco New Relic no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo New Relic.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png


