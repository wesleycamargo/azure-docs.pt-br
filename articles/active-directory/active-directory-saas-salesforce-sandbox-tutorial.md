---
title: "Tutorial: integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 128d04fdf191b60441b695efef2bf602920d80e6
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Active Directory do Azure com a Área Restrita Salesforce

Neste tutorial, você aprende a integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory).

A integração do Salesforce Sandbox ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso à Área Restrita Salesforce.
- Você pode permitir que os usuários se conectem automaticamente à Área Restrita Salesforce (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce Sandbox, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único na Área Restrita Salesforce

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Salesforce Sandbox por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria
Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Salesforce Sandbox por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Área Restrita Salesforce**, selecione **Área Restrita Salesforce** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Área Restrita Salesforce na lista de resultados](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com a Área Restrita Salesforce, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce Sandbox é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Na Área Restrita Salesforce, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Salesforce Sandbox, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste da Área Restrita Salesforce](#create-a-salesforce-sandbox-test-user)** – para ter um equivalente de Brenda Fernandes na Área Restrita Salesforce vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Salesforce Sandbox.

**Para configurar o logon único do Azure AD com o Salesforce Sandbox, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Salesforce Sandbox**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Na seção **Domínio e URLs do Salesforce Sandbox**, realize as seguintes etapas:

    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Salesforce Sandbox**, clique em **Configurar o Salesforce Sandbox** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Abra uma nova guia no navegador e faça logon em sua conta Administrador do Salesforce Sandbox.

8. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/configure1.png)

9. Role para baixo até **CONFIGURAÇÕES** no painel de navegação e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Para definir as configurações de logon único do SAML, clique em **Novo**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. Na seção Configurações de Logon Único de SAML, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-saml-config.png)

    a. Na caixa de texto **Nome**, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*). 

    b. No campo **Emissor**, cole o valor da **ID de Entidade do SAML** que você copiou do portal do Azure

    c. Na caixa de texto **ID da Entidade**, digite `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`, se esta for a primeira instância da Área Restrita Salesforce que você está adicionando ao diretório. Se você já tiver adicionado uma instância da Área restrita do Salesforce, para a **ID da Entidade**, digite a **URL de Logon** que deve estar no seguinte formato: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`  
 
    d. Para carregar o **Certificado de Provedor de Identidade**, clique em **Escolher Arquivo** para navegar e selecionar o arquivo do certificado que você baixou do portal do Azure.  

    e. Como **Tipo de Identidade SAML**, escolha uma das seguintes opções:
    
      * Selecione **A declaração contém o nome do usuário do Salesforce**, se o nome do usuário do Salesforce estiver sendo transmitido na declaração SAML

      * Selecione **A declaração contém a ID de Federação do objeto de Usuário**, se a ID de Federação do objeto de Usuário estiver sendo transmitida na declaração SAML

      * Selecione **A declaração contém a ID de Usuário do objeto de Usuário**, se a ID de Usuário do objeto de Usuário estiver sendo transmitida na declaração SAML
 
    f. Para **Local de Identidade SAML**, selecione **A identidade está no elemento NameIdentifier da instrução Subject**.

    g. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP Post**. 

    h. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único** copiado do portal do Azure. 

    i. O SFDC não dá suporte a logout SAML.  Como alternativa, cole `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` na caixa de texto  **URL de Logoff do Provedor de Identidade** .

    j. Clique em **Salvar**.

### <a name="enable-your-domain"></a>Habilitar seu domínio
Esta seção pressupõe que você já tenha criado um domínio.  Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para habilitar seu domínio, execute as seguintes etapas:**

1. No painel de navegação à esquerda no Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.
   
     ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-my-domain.png)
   
   >[!NOTE]
   >Verifique se o domínio foi configurado corretamente. 

2. Na seção **Configuração de Autenticação**, clique em **Editar** e, para o **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único SAML da seção anterior. Clique em **Salvar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Assim que você tiver um domínio configurado, seus usuários deverão usar a URL do domínio para fazer logon na área restrita Salesforce.  

Para obter o valor da URL, clique no perfil SSO criado na seção anterior.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-salesforce-sandbox-test-user"></a>Criar um usuário de teste da Área Restrita Salesforce

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.
Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Salesforce Sandbox.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Salesforce Sandbox, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Salesforce Sandbox**.

    ![O link da Área Restrita Salesforce na lista de Aplicativos](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco da Área Restrita Salesforce no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Área Restrita Salesforce.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_203.png

