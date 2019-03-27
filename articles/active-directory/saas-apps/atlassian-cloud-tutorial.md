---
title: 'Tutorial: Integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49d44fa0926afac917ae0ba355d37f13a354f432
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887927"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory ao Atlassian Cloud

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure AD (Azure Active Directory).
A integração do Atlassian Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Atlassian Cloud.
* Você pode habilitar os usuários a fazer logon automaticamente no Atlassian Cloud (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Atlassian Cloud, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Atlassian Cloud habilitada para logon único
* Para habilitar logon único do SAML (Security Assertion Markup Language) para produtos Atlassian Cloud, é necessário configurar o Atlassian Access. Saiba mais sobre o [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Atlassian Cloud é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria

Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Atlassian Cloud, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para configurar e testar o logon único do Azure AD com o Atlassian Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Atlassian Cloud](#configure-atlassian-cloud-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Atlassian Cloud](#create-atlassian-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no Atlassian Cloud que esteja vinculado à sua representação no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Atlassian Cloud, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Atlassian Cloud**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![[Nome do aplicativo] Informações de logon único de domínio e URLs](common/idp-relay.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/saml/<unique ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Você obterá esses valores reais na tela Configuração de SAML do Atlassian Cloud, explicada posteriormente no tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![[Nome do aplicativo] Informações de logon único de domínio e URLs](common/both-signonurl.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > O sinal de URL de Entrada anterior não é real. Atualize o valor com a URL de Entrada real. Contate a [Equipe de suporte ao cliente do Atlassian Cloud](https://support.atlassian.com/) para obter esse valor.

6. O aplicativo Atlassian Cloud espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Atlassian Cloud espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar Atlassian Cloud**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-atlassian-cloud-single-sign-on"></a>Configurar o logon único do Atlassian Cloud

1. Para configurar o SSO para seu aplicativo, faça logon no portal do Atlassian usando com as credenciais de administrador.

2. É necessário verificar o domínio antes de configurar logon único. Para obter mais informações, consulte o documento de [verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

3. No painel à esquerda, selecione **Logon único SAML**. Se você ainda não fez isso, assine o Identity Manager da Atlassian.

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. Na janela de **Adicionar configuração SAML**, faça o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

     a. Na caixa **ID da Entidade do Provedor de Identidade**, cole o ID da entidade SAML que você copiou do portal do Azure.

    b. Na caixa **URL de SSO do Provedor de Identidade**, cole a URL de serviço de logon único SAML que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure em arquivo .txt, copie o valor (sem as linhas *Begin Certificate* e *End Certificate*) e cole-os na caixa **Certificado X509 público**.

    d. Clique em **Salvar Configuração**.

5. Para garantir que você configurou as URLs corretas, atualize as configurações do Azure AD fazendo o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

     a. Na janela de SAML, copie a **ID de Entidade do SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **Identificador**.

    b. Na janela de SAML, copie a **URL do Serviço do Consumidor de Declaração de SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **URL de Resposta**. A URL de logon é a URL do locatário do seu Atlassian Cloud.

    > [!NOTE]
    > Se você for um cliente existente, depois de atualizar os valores da **ID de identidade SP** e **URL do Serviço de Consumidor de Declaração de SP** no portal do Azure, selecione **Sim, atualize a configuração**. Se você for um novo cliente, você pode ignorar esta etapa.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Atlassian Cloud.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Atlassian Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de Aplicativos, digite e selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Para enviar um convite por email para o usuário, selecione **Convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

> [!NOTE]
> Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Atlassian Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao Atlassian Cloud no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
