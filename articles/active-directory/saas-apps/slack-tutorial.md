---
title: 'Tutorial: Integração do Azure Active Directory ao Slack | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: jeedes
ms.openlocfilehash: 5b1099e46cf1aa2fd4b948fee8407cfd859390ce
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129107"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integração do Active Directory do Azure com o Slack

Neste tutorial, você aprenderá a integrar o Slack ao Azure AD (Azure Active Directory).

A integração do Slack ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Slack
- Você pode permitir que seus usuários façam logon automaticamente no Slack (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Slack, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Slack

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Slack da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-slack-from-the-gallery"></a>Adicionar o Slack da galeria
Para configurar a integração do Slack ao Azure AD, você precisa adicionar o Slack por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Slack por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Slack**.

    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/tutorial_slack_search.png)

1. No painel de resultados, selecione **Slack** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Slack, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Slack é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Slack.

No Slack, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Slack, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Slack](#creating-a-slack-test-user)** – para ter um equivalente de Brenda Fernandes no Slack que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Slack.

**Para configurar o logon único do Azure AD com o Slack, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Slack**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Na seção **URLs e Domínio do Slack**, execute as seguintes etapas:

    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.slack.com`

    b. Na caixa de texto **Identificador**,atualize o valor de URL do logon único. Este é seu domínio de espaço de trabalho. Por exemplo: `https://contoso.slack.com`

1. O aplicativo Slack espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Se você tiver usuários com **endereços de email**  atribuídos que não estejam em uma licença do Office365, a declaração **User.Email** não será exibida no token SAML. Nesses casos, é recomendável usar **user.userprincipalname** como o valor do atributo **User.Email** para mapear como **Identificador Exclusivo**.

1. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, selecione **user.mail** como **Identificador de Usuário** e, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |  
    | User.Username | user.userprincipalname |

    a. Clique em **Atributo** para abrir a caixa de diálogo **Editar Atributo** e realize as seguintes etapas:

    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    b. Na lista **Valor**, selecione o valor de atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Clique em **OK**

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/slack-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Slack**, clique em **Configurar o Slack** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_configure.png)

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Slack como administrador.

1. Navegue até **Microsoft Azure AD** e depois em **Configurações de Equipe**.

     ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_001.png)

1. Na seção **Configurações de Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_002.png)

1. No diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Na caixa de texto **Ponto de Extremidade do SAML 2.0 (HTTP)**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    b.  Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    c.  Abra seu arquivo de certificado baixado no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público**.

    d. Defina as três configurações acima conforme apropriado para sua equipe do Slack. Para saber mais sobre as configurações, encontre o **guia de configuração de SSO do Slack** aqui. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Clique em **Salvar Configuração**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/create_aaduser_03.png)

1. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/slack-tutorial/create_aaduser_04.png)

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-slack-test-user"></a>Criar um usuário de teste do Slack

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Slack. O Slack dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Slack, caso ele ainda não exista. O Slack também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](slack-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure Active Directory Connect é a ferramenta de sincronização que pode sincronizar no local de identidades do Active Directory para o Azure AD e, em seguida, esses usuários sincronizados também podem usar os aplicativos como outros usuários de nuvem.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Slack.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Slack, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Slack**.

    ![Configurar o logon único](./media/slack-tutorial/tutorial_slack_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Slack no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de Slack.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
