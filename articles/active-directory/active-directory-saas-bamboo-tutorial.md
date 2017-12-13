---
title: "Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Bamboo da Resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 3195dee2df560993e7885d138168e4514272f7eb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Bamboo da Resolution GmbH com o Azure AD (Azure Active Directory).

A integração de SSO do SAML para Bamboo da Resolution GmbH com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao SSO do SAML para Bamboo da Resolution GmbH.
- Você pode habilitar os usuários a entrarem automaticamente no SSO do SAML para Bamboo da Resolution GmbH (logon único) com as próprias contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SSO do SAML para Bamboo da Resolution GmbH

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SSO do SAML para Bamboo da Resolution GmbH da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adicionar o SSO do SAML para Bamboo da Resolution GmbH da galeria
Para configurar a integração do SSO do SAML para Bamboo da Resolution GmbH no Azure AD, adicione o SSO do SAML para Bamboo da Resolution GmbH da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar SSO do SAML para Bamboo da Resolution GmbH da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SSO do SAML para Bamboo da Resolution GmbH**, selecione **SSO do SAML para Bamboo da Resolution GmbH** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SSO do SAML para Bamboo da Resolution GmbH na lista de resultados](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH com base em um usuário de teste chamado "Brenda Fernandes".

Para o logon único funcionar, o Azure AD precisa saber que o usuário da contraparte no SSO do SAML para Bamboo da Resolution GmbH é para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para Bamboo da Resolution GmbH precisa ser estabelecida.

No SSO do SAML para Bamboo da Resolution GmbH, atribua o valor de **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, conclua os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste de SSO do SAML para Bamboo da Resolution GmbH](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  – para ter um equivalente de Brenda Fernandes no SSO do SAML para Bamboo da Resolution GmbH vinculado à representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo SSO do SAML para Bamboo da Resolution GmbH.

**Para configurar o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativo **SSO do SAML para Bamboo da Resolution GmbH**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlbase.png)

3. Na seção **Domínio e URLs do SSO do SAML para Bamboo da Resolution GmbH**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo IdP:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Bamboo da Resolution GmbH](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Bamboo da Resolution GmbH](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a [equipe de suporte ao Cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) para obter esses valores. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-bamboo-tutorial/tutorial_general_400.png)

7. Faça logon no site da empresa do SAML SSO para Bamboo da Resolution GmbH como administrador.

8. No lado direito da barra de ferramentas principal, clique em **configurações** > **complementos**.

    ![As configurações](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_setings.png)

9. Vá para a seção SEGURANÇA, clique em **logon único do SAML** na barra de menus.

    ![O Samlsingle](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlsingle.png)

10. Na **página de configuração do plug-in de logon único do SAML**, clique em **Adicionar idp**. 

    ![Adicionar idp](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addidp.png)

11. Na página **Escolher seu Provedor de Identidade SAML**, execute as seguintes etapas:

    ![O provedor de identidade](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecione **Tipo de Idp** como **AD do AZURE**.

    b. Na caixa de texto **Nome**, digite um nome.

    c. Na caixa de texto **Descrição**, digite a descrição.

    d. Clique em **Avançar**.

12. Na página **Configuração do provedor de identidade**, clique no botão **Avançar**.

    ![As configurações de identidade](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityconfig.png)

13.  Na página **importar metadados de Idp do SAML**, clique em **carregar arquivo** para carregar o arquivo **METADATA XML** que você fez o download do Portal do Azure.

    ![O idpmetadata](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

14. Clique em **Avançar**.

15. Clique em **Salvar configurações**.

    ![Salvar](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-bamboo-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-bamboo-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-bamboo-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-bamboo-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Criando um usuário de teste do SSO do SAML para Bamboo da Resolution GmbH

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SSO do SAML para Bamboo da Resolution GmbH. O SSO do SAML para Bamboo da Resolution GmbH dá suporte ao provisionamento just-in-time e os usuários podem ser criados manualmente, entre em contato com a [equipe de suporte ao cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support), de acordo com as suas necessidades.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, habilite Britta Simon a usar o logon único do Azure concedendo acesso ao SSO do SAML para Bamboo da Resolution GmbH.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao SSO do SAML para Bamboo da Resolution GmbH, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SSO do SAML para Bamboo da Resolution GmbH**.

    ![O SSO do SAML para Bamboo da Resolution GmbH, link na lista de Aplicativos](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco SSO do SAML para Bamboo da Resolution GmbH no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SSO do SAML para Bamboo da Resolution GmbH.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_203.png

