---
title: 'Tutorial: Integração do Azure Active Directory com o TextMagic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: b8ffd732221604d55c65d4623de89f716bba49eb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427451"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Tutorial: Integração do Azure Active Directory ao TextMagic

Neste tutorial, você aprenderá a integrar o TextMagic ao Azure AD (Azure Active Directory).

A integração do TextMagic ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TextMagic.
- Você pode permitir que seus usuários façam logon automaticamente no TextMagic (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TextMagic, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TextMagic

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando TextMagic da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-textmagic-from-the-gallery"></a>Adicionando TextMagic da galeria
Para configurar a integração do TextMagic ao Azure AD, você precisa adicionar o TextMagic da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TextMagic da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **TextMagic**, selecione **TextMagic** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![TextMagic na lista de resultados](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o TextMagic com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TextMagic é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TextMagic.

No TextMagic, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TextMagic, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do TextMagic](#create-a-textmagic-test-user)** – para ter um equivalente de Brenda Fernandes no TextMagic vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo TextMagic.

**Para configurar o logon único do Azure AD com o TextMagic, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **TextMagic**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/textmagic-tutorial/tutorial_textmagic_samlbase.png)

1. Na seção **Domínio e URLs do TextMagic**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de URLs e Domínio TextMagic](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://my.textmagic.com/saml/metadata`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de URLs e Domínio TextMagic](./media/textmagic-tutorial/url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://my.textmagic.com/login/sso`


1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/textmagic-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do TextMagic**, clique em **Configurar TextMagic** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do TextMagic](./media/textmagic-tutorial/tutorial_textmagic_configure.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa TextMagic como administrador.

1. Selecione **Configurações da conta** sob o nome de usuário.

    ![Configuração do TextMagic](./media/textmagic-tutorial/config1.png) 
1. Clique na guia **"SSO (Logon Único)"** e preencha os campos a seguir:  
    
    ![Configuração do TextMagic](./media/textmagic-tutorial/config2.png)

    a. Na caixa de texto **ID da Entidade do provedor de Identidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    b. Na caixa de texto **URL de SSO do provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único** copiado do portal do Azure.

    c. Na caixa de texto **URL de SLO do provedor de Identidade**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    d. Abra seu certificado codificado em **base-64** no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado Público x509**.

    e. Clique em **Salvar**.


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/textmagic-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/textmagic-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/textmagic-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/textmagic-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-textmagic-test-user"></a>Criar um usuário de teste do TextMagic

O aplicativo dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão automaticamente criados no aplicativo. Você precisa preencher as informações uma vez no primeiro logon para ativar a subconta no sistema.
Não há itens de ação para você nesta seção.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TextMagic.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao TextMagic, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **TextMagic**.

    ![O link TextMagic na lista de Aplicativos](./media/textmagic-tutorial/tutorial_textmagic_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco TextMagic no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo TextMagic.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/textmagic-tutorial/tutorial_general_01.png
[2]: ./media/textmagic-tutorial/tutorial_general_02.png
[3]: ./media/textmagic-tutorial/tutorial_general_03.png
[4]: ./media/textmagic-tutorial/tutorial_general_04.png

[100]: ./media/textmagic-tutorial/tutorial_general_100.png

[200]: ./media/textmagic-tutorial/tutorial_general_200.png
[201]: ./media/textmagic-tutorial/tutorial_general_201.png
[202]: ./media/textmagic-tutorial/tutorial_general_202.png
[203]: ./media/textmagic-tutorial/tutorial_general_203.png

