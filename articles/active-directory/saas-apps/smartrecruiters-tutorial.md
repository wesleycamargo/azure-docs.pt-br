---
title: 'Tutorial: Integração do Azure Active Directory ao SmartRecruiters | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d2fc7e79e92f3a1c9e069a652b2a3c21183bf1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190096"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Tutorial: Integração do Azure Active Directory ao SmartRecruiters

Neste tutorial, você aprenderá a integrar o SmartRecruiters ao Azure AD (Azure Active Directory).

A integração do SmartRecruiters ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SmartRecruiters.
- Você pode permitir que os usuários façam logon automaticamente no SmartRecruiters (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SmartRecruiters, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do SmartRecruiters

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SmartRecruiters da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-smartrecruiters-from-the-gallery"></a>Adicionando SmartRecruiters da galeria
Para configurar a integração do SmartRecruiters ao Azure AD, você precisará adicionar o SmartRecruiters da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SmartRecruiters da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **SmartRecruiters**, selecione **SmartRecruiters** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SmartRecruiters na lista de resultados](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SmartRecruiters, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SmartRecruiters é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SmartRecruiters.

No SmartRecruiters, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SmartRecruiters, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do SmartRecruiters](#create-a-smartrecruiters-test-user)** – para ter um equivalente de Brenda Fernandes no SmartRecruiters vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SmartRecruiters.

**Para configurar o logon único do Azure AD com o SmartRecruiters, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **SmartRecruiters**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

1. Na seção **Domínio e URLs do SmartRecruiters**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de URLs e Domínio do SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de URLs e Domínio do SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte do cliente SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) para obter esses valores. 

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o certificado no computador.

    ![O link de download do Certificado](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/smartrecruiters-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do SmartRecruiters**, clique em **Configurar o SmartRecruiters** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração de SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do SmartRecruiters como um administrador.

1. Vá para **Configurações/Admin**.

    ![Configuração de SmartRecruiters](./media/smartrecruiters-tutorial/configure.png)

1. Na seção **Configuração**, clique em **SSO Web**.

    ![Configuração de SmartRecruiters](./media/smartrecruiters-tutorial/configure1.png)

1. Mude **Habilitar SSO da Web**.

    ![Configuração de SmartRecruiters](./media/smartrecruiters-tutorial/configure2.png)

1. Em **Configuração do Provedor de Identidade**, execute as seguintes etapas:

    ![Configuração de SmartRecruiters](./media/smartrecruiters-tutorial/configure4.png)

     a. Na caixa de texto **URL do Provedor de Identidade**, cole o valor da **URL de Serviço de Logon Único do SAML** copiado do portal do Azure.

    b. Abra **certificate(Base64)** que você baixou do portal do Azure e cole o valor na caixa de texto **certificado do Provedor de Identidade**.

1. Clique em **Salvar configuração de SSO Web**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/smartrecruiters-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/smartrecruiters-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/smartrecruiters-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/smartrecruiters-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-smartrecruiters-test-user"></a>Criar um usuário de teste do SmartRecruiters

Nesta seção, você criará um usuário chamado Brenda Fernandes no SmartRecruiters. Trabalhe com a  [equipe de suporte do SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/) para adicionar os usuários à plataforma SmartRecruiters. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SmartRecruiters.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SmartRecruiters, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **SmartRecruiters**.

    ![O link do SmartRecruiters na lista de Aplicativos](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SmartRecruiters no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo SmartRecruiters.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/smartrecruiters-tutorial/tutorial_general_203.png

