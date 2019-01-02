---
title: 'Tutorial: Integração do Azure Active Directory ao Five9 Plus Adapter (CTI, Contact Center Agents) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Five9 Plus Adapter (CTI, Contact Center Agents).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: c8d3364f9de298d19df35846af5f421978fea960
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842162"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Integração do Azure Active Directory ao Five9 Plus Adapter (CTI, Contact Center Agents)

Neste tutorial, você aprende a integrar o Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD (Azure Active Directory).

A integração do Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Five9 Plus Adapter (CTI, Contact Center Agents)
- É possível permitir que os usuários se conectem automaticamente ao Five9 Plus Adapter (CTI, Contact Center Agents) (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Five9 Plus Adapter (CTI, Contact Center Agents), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Five9 Plus Adapter (CTI, Contact Center Agents)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Microsoft Azure Active Directory, você poderá obter uma versão de avaliação de um mês aqui: [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Five9 Plus Adapter (CTI, Contact Center Agents) por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Adicionando o Five9 Plus Adapter (CTI, Contact Center Agents) por meio da galeria
Para configurar a integração do Five9 Plus Adapter (CTI, Contact Center Agents) ao Azure AD, é necessário adicionar o Five9 Plus Adapter (CTI, Contact Center Agents) à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Five9 Plus Adapter (CTI, Contact Center Agents) por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/tutorial_five9_search.png)

1. No painel de resultados, selecione **Five9 Plus Adapter (CTI, Contact Center Agents)** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Five9 Plus Adapter (CTI, Contact Center Agents), com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Five9 Plus Adapter (CTI, Contact Center Agents) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Five9 Plus Adapter (CTI, Contact Center Agents).

No Five9 Plus Adapter (CTI, Contact Center Agents), atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Five9 Plus Adapter (CTI, Contact Center Agents), você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Five9 Plus Adapter (CTI, Contact Center Agents)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)** – para ter um equivalente de Brenda Fernandes no Five9 Plus Adapter (CTI, Contact Center Agents) que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Five9 Plus Adapter (CTI, Contact Center Agents).

**Para configurar o logon único do Azure AD com o Five9 Plus Adapter (CTI, Contact Center Agents), realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Five9 Plus Adapter (CTI, Contact Center Agents)**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. Na seção **Domínio e URLs do Five9 Plus Adapter (CTI, Contact Center Agents)**, realize as seguintes etapas:

    ![Configurar o logon único](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. Na caixa de texto **Identificador**, digite uma URL usando os seguintes padrões:

    |    Ambiente      |       URL      |
    | :-- | :-- |
    | Para o “Five9 Plus Adapter for Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Para o “Five9 Plus Adapter for Zendesk” | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Para o “Five9 Plus Adapter for Agent Desktop Toolkit” | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    |      Ambiente     |      URL      |
    | :--                  | :--           |
    | Para o “Five9 Plus Adapter for Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Para o “Five9 Plus Adapter for Zendesk” | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Para o “Five9 Plus Adapter for Agent Desktop Toolkit” | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/five9-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Five9 Plus Adapter (CTI, Contact Center Agents)**, clique em **Configurar o Five9 Plus Adapter (CTI, Contact Center Agents)** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/five9-tutorial/tutorial_five9_configure.png) 

1. Para configurar o logon único no lado do **Five9 Plus Adapter (CTI, Contact Center Agents)**, é necessário enviar o **Certificado (Base64) baixado, a URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** para a [equipe de suporte do Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact). Além disso, para configurar o SSO adicional, siga as etapas abaixo de acordo com o adaptador:

    a. Guia do Administrador “Five9 Plus Adapter for Agent Desktop Toolkit”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Guia do administrador “Five9 Plus Adapter for Microsoft Dynamics CRM”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Guia do administrador“Five9 Plus Adapter for Zendesk”: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/five9-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Criando um usuário de teste do Five9 Plus Adapter (CTI, Contact Center Agents)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Five9 Plus Adapter (CTI, Contact Center Agents). Trabalhe com a  [equipe de suporte do withFive9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact)  para adicionar os usuários à plataforma Five9 Plus Adapter (CTI, Contact Center Agents). Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Five9 Plus Adapter (CTI, Contact Center Agents).

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Five9 Plus Adapter (CTI, Contact Center Agents), realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![Configurar o logon único](./media/five9-tutorial/tutorial_five9_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Five9 Plus Adapter (CTI, Contact Center Agents) no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Five9 Plus Adapter (CTI, Contact Center Agents).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

