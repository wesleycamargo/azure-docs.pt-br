---
title: "Tutorial: Integração do Azure Active Directory ao Mimecast Personal Portal | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Mimecast Personal Portal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: 0b33ca8933f1dc3e9439614570c81784f28186b6
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integração do Azure Active Directory ao Mimecast Personal Portal

Neste tutorial, você aprende a integrar o Mimecast Personal Portal ao Azure AD (Azure Active Directory).

A integração do Mimecast Personal Portal ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Mimecast Personal Portal.
- É possível permitir que os usuários se conectem automaticamente ao Mimecast Personal Portal (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mimecast Personal Portal, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Mimecast Personal Portal com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Mimecast Personal Portal por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adicionando o Mimecast Personal Portal por meio da galeria
Para configurar a integração do Mimecast Personal Portal ao Azure AD, é necessário adicionar o Mimecast Personal Portal à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Mimecast Personal Portal por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Mimecast Personal Portal**, selecione **Mimecast Personal Portal** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Mimecast Personal Portal na lista de resultados](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Mimecast Personal Portal, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Mimecast Personal Portal é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mimecast Personal Portal.

No Mimecast Personal Portal, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Mimecast Personal Portal, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Mimecast Personal Portal](#create-a-mimecast-personal-portal-test-user)** – Para ter um equivalente de Brenda Fernandes no Mimecast Personal Portal que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Mimecast Personal Portal.

**Para configurar o logon único do Azure AD com o Mimecast Personal Portal, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Mimecast Personal Portal**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Na seção **Domínio e URLs do Mimecast Personal Portal**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Exterior        | `https://jer-api.mimecast.com/login/saml`|

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Exterior        | `https://jer-api.mimecast.com/sso/<accountcode>`|
    
    > [!NOTE] 
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contate a [equipe de suporte ao Cliente do Mimecast Personal Portal](http://www.mimecast.com/customer-success/technical-support/) para obter o valor. 

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Mimecast Personal Portal**, clique em **Configurar o Mimecast Personal Portal** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu Mimecast Personal Portal como um administrador.

8. Acesse **Serviços \> Aplicativos**.
   
    ![Aplicativos](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "Aplicativos")

9. Clique em **Perfis de Autenticação**.
   
    ![Perfis de Autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "Perfis de Autenticação")

10. Clique em **Novo Perfil de Autenticação**.
   
    ![Novo Perfil de Autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "Novo Perfil de Autenticação")

11. Na seção **Perfil de Autenticação** , realize as seguintes etapas:
   
    ![Perfil de Autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "Perfil de Autenticação")
   
    a. Na caixa de texto **Descrição** , digite um nome para a sua configuração.
   
    b. Selecione **Impor Autenticação SAML para o Mimecast Personal Portal**.
   
    c. Como **Provedor**, selecione **Azure Active Directory**.
   
    d. Na caixa de texto **URL do Emissor**, cole o valor da **ID da Entidade SAML** copiado do Portal do Azure.
   
    e. Na caixa de texto **URL de Logon**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.
   
    f. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    g. Abra o certificado codificado em **Base64** baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado do Provedor de Identidade (Metadados)**.

    h. Selecione **Permitir Logon Único**.
   
    i. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Criar um usuário de teste do Mimecast Personal Portal

Para permitir que os usuários do AD do Azure façam logon no Mimecast Personal Portal, eles devem ser provisionados no Mimecast Personal Portal. No caso do Mimecast Personal Portal, o provisionamento é uma tarefa manual.

Você precisa registrar um domínio antes de criar usuários.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon no **Mimecast Personal Portal** como administrador.

2. Vá para **Diretórios \> Interno**.
   
    ![Diretórios](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "Diretórios")

3. Clique em **Registrar Novo Domínio**.
   
    ![Registrar Novo Domínio](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "Registrar Novo Domínio")

4. Depois de criar o novo domínio, clique em **Novo Endereço**.
   
    ![Novo Endereço](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "Novo Endereço")

5. Na caixa de diálogo Novo endereço, realize as seguintes etapas de uma conta válida do Azure AD que você deseja provisionar:
   
    ![Salvar](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "Salvar")
   
    a. Na caixa de texto **Endereço de Email**, digite o **Endereço de Email** do usuário como **BrittaSimon@contoso.com**.
    
    b. Na caixa de texto **Nome Global**, digite o **nome de usuário** como **BrendaFernandes**.

    c. Nas caixas de texto **Senha** e **Confirmar Senha**, digite a **Senha** do usuário.
   
    b. Clique em **Salvar**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Mimecast Personal Portal ou as APIs fornecidas pelo Mimecast Personal Portal para provisionar contas de usuário do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Mimecast Personal Portal.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Mimecast Personal Portal, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Mimecast Personal Portal**.

    ![Link do Mimecast Personal Portal na lista de Aplicativos](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Mimecast Personal Portal no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Mimecast Personal Portal.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

