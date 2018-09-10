---
title: 'Tutorial: Integração do Azure Active Directory ao InTime | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o InTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d4e2c6e1-ae5d-4d2c-8ffc-1b24534d376a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: jeedes
ms.openlocfilehash: ee1e1aacec37db476aa5a295c9a3cd84e071a9d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432558"
---
# <a name="tutorial-azure-active-directory-integration-with-intime"></a>Tutorial: Integração do Azure Active Directory ao InTime

Neste tutorial, você aprende a integrar o InTime ao Azure Active Directory (Azure AD).

A integração do InTime ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao InTime.
- Você pode permitir que seus usuários entrem automaticamente no InTime (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao InTime, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do InTime

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do InTime a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-intime-from-the-gallery"></a>Adição do InTime a partir da galeria
Para configurar a integração do InTime ao Azure AD, você precisa adicionar o InTime por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o InTime por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **InTime**, selecione **InTime** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![InTime na lista de resultados](./media/intime-tutorial/tutorial_intime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o InTime, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do InTime é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no InTime.

No InTime, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o InTime, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar a partir de um usuário de teste do InTime](#create-a-intime-test-user)** – para ter um equivalente de Brenda Fernandes no InTime vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo InTime.

**Para configurar o logon único do Azure AD com o InTime, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **InTime**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/intime-tutorial/tutorial_intime_samlbase.png)

1. Na seção **URLs e Domínio do InTime**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do InTime](./media/intime-tutorial/tutorial_intime_url.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://intime6.intimesoft.com/mytime/login/login.xhtml`

    b. Na caixa de texto **Identificador**, digite a URL: `https://auth.intimesoft.com/auth/realms/master`

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/intime-tutorial/tutorial_intime_certificate.png) 

1. Seu aplicativo InTime espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador de usuário** é **user.userprincipalname**, mas o InTime espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user.mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização 

    ![Configurar Atributo](./media/intime-tutorial/tutorial_intime_attribute.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/intime-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do InTime**, clique em **Configurar o InTime** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do InTime](./media/intime-tutorial/tutorial_intime_configure.png) 

1. Para configurar o logon único no lado do **InTime**, é necessário enviar o **XML de metadados**, a **URL de Saída e a URL do Serviço de Logon Único SAML** baixados para a [Equipe de suporte do InTime](mailto:hdollard@intimesoft.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/intime-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/intime-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/intime-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/intime-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-intime-test-user"></a>Criar um usuário de teste do InTime

Nesta seção, você cria um usuário chamado Brenda Fernandes no InTime. Trabalhe com a [equipe de suporte do InTime](mailto:hdollard@intimesoft.com) para adicionar os usuários na plataforma do InTime. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao InTime.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao InTime, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **InTime**.

    ![O link do InTime na lista de Aplicativos](./media/intime-tutorial/tutorial_intime_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco InTime no Painel de Acesso, você deve acessar a página de logon do aplicativo InTime. Clique no botão **Login**, uma série de IdPs serão exibidos em uma lista de botões. Clique no **nome IDP** determinado pela [equipe de suporte do InTime](mailto:hdollard@intimesoft.com) para fazer logon no seu aplicativo InTime. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/intime-tutorial/tutorial_general_01.png
[2]: ./media/intime-tutorial/tutorial_general_02.png
[3]: ./media/intime-tutorial/tutorial_general_03.png
[4]: ./media/intime-tutorial/tutorial_general_04.png

[100]: ./media/intime-tutorial/tutorial_general_100.png

[200]: ./media/intime-tutorial/tutorial_general_200.png
[201]: ./media/intime-tutorial/tutorial_general_201.png
[202]: ./media/intime-tutorial/tutorial_general_202.png
[203]: ./media/intime-tutorial/tutorial_general_203.png

