---
title: "Tutorial: integração do Azure Active Directory ao IMPAC Risk Manager | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o IMPAC Risk Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: ade4076917988c5747a0d10a99578b49c917e1db
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Tutorial: integração do Azure Active Directory ao IMPAC Risk Manager

Neste tutorial, você aprenderá a integrar o IMPAC Risk Manager ao Azure AD (Azure Active Directory).

A integração do IMPAC Risk Manager ao Azure AD lhe oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao IMPAC Risk Manager.
- Você pode permitir que os usuários façam logon automaticamente no IMPAC Risk Manager (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o IMPAC Risk Manager, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do IMPAC Risk Manager habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o IMPAC Risk Manager da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Adicionando o IMPAC Risk Manager da galeria
Para configurar a integração do IMPAC Risk Manager ao Azure AD, você precisará adicionar o IMPAC Risk Manager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o IMPAC Risk Manager da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **IMPAC Risk Manager**, selecione **IMPAC Risk Manager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![IMPAC Risk Manager na lista de resultados](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o IMPAC Risk Manager, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário no IMPAC Risk Manager é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no IMPAC Risk Manager.

No IMPAC Risk Manager, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o IMPAC Risk Manager, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do IMPAC Risk Manager](#create-a-impac-risk-manager-test-user)** – para ter um equivalente de Brenda Fernandes no IMPAC Risk Manager vinculado à representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo IMPAC Risk Manager.

**Para configurar o logon único do Azure AD com o IMPAC Risk Manager, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **IMPAC Risk Manager**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

3. Na seção **Domínio e URLs do IMPAC Risk Manager**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo IdP:

    ![Informações de logon único em Domínio e URLs do IMPAC Risk Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. Na caixa de texto **Identificador**, digite um valor fornecido pelo IMPAC

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | Ambiente | Padrão de URL |
    | ---------------|--------------- |    
    | Para produção |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para preparo e treinamento  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para desenvolvimento  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para garantia de qualidade |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para teste |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do IMPAC Risk Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    | Ambiente | Padrão de URL |
    | ---------------|--------------- |    
    | Para produção |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para preparo e treinamento  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para desenvolvimento  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para garantia de qualidade |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para teste |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com [equipe de suporte do cliente do IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz) para obter esses valores.

5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do IMPAC Risk Manager**, clique em **Configurar IMPAC Risk Manager** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único SAML, a ID da Entidade SAML** e a **URL de saída** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

8. Para configurar o logon único no lado do **IMPAC Risk Manager**, é necessário enviar o **Certificado (Base64)** baixado, a **URL de Saída, ID da Entidade SAML** e **URL do Serviço de Logon Único SAML** para a [equipe de suporte do IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Criar um usuário de teste do IMPAC Risk Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no IMPAC Risk Manager. Trabalhe com a [equipe de suporte do IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz) para adicionar os usuários na plataforma do IMPAC Risk Manager. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao IMPAC Risk Manager.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao IMPAC Risk Manager, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **IMPAC Risk Manager**.

    ![O link do IMPAC Risk Manager na lista de Aplicativos](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IMPAC Risk Manager no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo IMPAC Risk Manager.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_203.png

