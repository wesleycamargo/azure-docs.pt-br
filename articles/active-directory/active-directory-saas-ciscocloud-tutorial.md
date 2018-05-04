---
title: 'Tutorial: integração do Azure Active Directory com o Cisco Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db1cea1d-ff0a-4f0d-b5fd-50ca32702d56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 8f3d97e26b71c2e17753943f7004d7eb7ee19e44
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-cloud"></a>Tutorial: integração do Azure Active Directory com o Cisco Cloud

Neste tutorial, você aprenderá como integrar o Cisco Cloud ao Azure Active Directory (Azure AD).

A integração do Cisco Cloud com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem tem acesso ao Cisco Cloud.
- Você pode permitir que os usuários sejam automaticamente conectados ao Cisco Cloud (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o Cisco Cloud, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Cisco Cloud

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Cisco Cloud da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-cisco-cloud-from-the-gallery"></a>Adicionando o Cisco Cloud da galeria
Para configurar a integração do Cisco Cloud com o Azure AD, você precisa adicionar o Cisco Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Cisco Cloud**, selecione **Cisco Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Cisco Cloud na lista de resultados](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Cisco Cloud com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Cisco Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Cisco Cloud.

Para configurar e testar o logon único do Azure AD com o Cisco Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Cisco Cloud](#create-a-cisco-cloud-test-user)**  - para ter um equivalente de Brenda Fernandes no Cisco Cloud vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo Cisco Cloud.

**Para configurar o logon único do Azure AD com o Cisco Cloud, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Cisco Cloud**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_samlbase.png)

3. Na seção **URLs e Domínio do Cisco Cloud**, se desejar configurar o aplicativo em modo iniciado pelo **IDP**, siga as etapas abaixo:

    ![Informações de logon único em Domínio e URLs do Cisco Cloud](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `<subdomain>.cisco.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.cisco.com/sp/ACS.saml2`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Cisco Cloud](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://<subdomain>.cloudapps.cisco.com`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Cisco Cloud](mailto:cpr-ops@cisco.com) para obter esses valores.

5. Na seção **Certificado de Autenticação SAML** clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_certificate.png)

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_400.png)

7. Para configurar logon único no **Cisco Cloud** é necessário enviar a **URL de Metadados de Federação de Aplicativos** para a [Equipe de suporte do Cisco Cloud](mailto:cpr-ops@cisco.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-cisco-cloud-test-user"></a>Criar um usuário de teste do Cisco Cloud

Nesta seção, você criará um usuário chamado Brenda Fernandes no Cisco Cloud. Trabalhe com [equipe de suporte do Cisco Cloud](mailto:cpr-ops@cisco.com) para adicionar os usuários na plataforma Cisco Cloud. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Cisco Cloud.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Cisco Cloud, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Cisco Cloud**.

    ![O link do Cisco Cloud na lista de Aplicativos](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Cisco Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Cisco Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_203.png

