---
title: 'Tutorial: integração do Azure Active Directory com o Workstars | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a9c1d13c1d51eeadae3ef9dad71e64618d1a5fe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444694"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Tutorial: integração do Azure Active Directory com o Workstars

Neste tutorial, você aprenderá a integrar o Workstars ao Azure AD (Azure Active Directory).

A integração do Workstars ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Workstars.
- Você pode permitir que os usuários façam logon automaticamente no Workstars (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Workstars, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Workstars habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Workstars da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-workstars-from-the-gallery"></a>Adicionar o Workstars da galeria
Para configurar a integração do Workstars ao Azure AD, você precisará adicionar o Workstars da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workstars da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Workstars**, selecione **Workstars** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workstars na lista de resultados](./media/workstars-tutorial/tutorial_workstars_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Workstars, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Workstars é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workstars.

No Workstars, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Workstars, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Workstars](#create-a-workstars-test-user)** – para ter um equivalente de Brenda Fernandes no Workstars vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Workstars.

**Para configurar o logon único do Azure AD com o Workstars, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Workstars**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/workstars-tutorial/tutorial_workstars_samlbase.png)

1. Na seção **Domínio e URLs do Workstars**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Workstars](./media/workstars-tutorial/tutorial_workstars_url.png)

    a. Na caixa de texto **Identificador**, digite a URL: `https://workstars.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte do Workstars](https://support.workstars.com) para obter o valor.
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/workstars-tutorial/tutorial_workstars_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/workstars-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Workstars**, clique em **Configurar o Workstars** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Workstars](./media/workstars-tutorial/tutorial_workstars_configure.png) 

1. Em outra janela do navegador, entre em seu site de empresa do Workstars como administrador.

1. Na barra de ferramentas principal, clique em **Configurações**.

    ![Configurações do Workstars](./media/workstars-tutorial/tutorial_workstars_sett.png)

1. Vá para **Logon** > **Configurações**.

    ![Logon do Workstars](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Configurações do Workstars](./media/workstars-tutorial/tutorial_workstars_settings.png)

1. Na página **Configurações de Logon Único (SAML) – Configurações**, realize as seguintes etapas:
    
    ![SAML do Workstars](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Na caixa de texto **Nome do Provedor de Identidade**, digite **Office 365**.

    b. Na caixa de texto **ID da Entidade do Provedor de Identidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    c. Copie o conteúdo do arquivo de certificado baixado no bloco de notas e então cole-o na caixa de texto **Certificado x509**. 

    d. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.
    
    e. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Saída** que você copiou do Portal do Azure. 

    f. Selecione **ID de Nome** como **Email (Padrão)**.

    g. Clique em **Confirmar**.
    
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/workstars-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/workstars-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/workstars-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/workstars-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-workstars-test-user"></a>Criar um usuário de teste do Workstars

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Workstars. Trabalhe com a [equipe de suporte do Workstars](https://support.workstars.com) para adicionar os usuários na plataforma do Workstars.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Workstars.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Workstars, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Workstars**.

    ![O link do Workstars na lista de Aplicativos](./media/workstars-tutorial/tutorial_workstars_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Workstars no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Workstars.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workstars-tutorial/tutorial_general_01.png
[2]: ./media/workstars-tutorial/tutorial_general_02.png
[3]: ./media/workstars-tutorial/tutorial_general_03.png
[4]: ./media/workstars-tutorial/tutorial_general_04.png

[100]: ./media/workstars-tutorial/tutorial_general_100.png

[200]: ./media/workstars-tutorial/tutorial_general_200.png
[201]: ./media/workstars-tutorial/tutorial_general_201.png
[202]: ./media/workstars-tutorial/tutorial_general_202.png
[203]: ./media/workstars-tutorial/tutorial_general_203.png

