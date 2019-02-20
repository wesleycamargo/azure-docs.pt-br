---
title: 'Tutorial: Integração do Azure Active Directory com o Meta Networks Connector | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4d702a33742da5501be208154cbdd593fa0524
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199698"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração do Azure Active Directory com o Meta Networks Connector

Neste tutorial, você aprenderá a integrar o Meta Networks Connector ao Azure AD (Azure Active Directory).

A integração do Meta Networks Connector com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Meta Networks Connector.
- Você pode permitir que seus usuários entrem automaticamente no Meta Networks Connector (Logon Único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Meta Networks Connector, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Meta Networks Connector

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Meta Networks Connector da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionando o Meta Networks Connector da galeria
Para configurar a integração do Meta Networks Connector ao Azure AD, você precisa adicionar o Meta Networks Connector da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Meta Networks Connector da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 
    
    ![O botão Azure Active Directory][1]
    
1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.
    
    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.
    
    ![O botão Novo aplicativo][3]
    
1. Na caixa de pesquisa, digite **Meta Networks Connector**, selecione **Meta Networks Connector** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.
    
    ![Meta redes conector na lista de resultados](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Meta Networks Connector com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte no Meta Networks Connector é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado no Meta Networks Connector precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Meta Networks Connector, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Crie um usuário de teste do Meta Networks Connector](#create-a-meta-networks-connector-test-user)** - para ter um equivalente de Britta Simon no Meta Networks Connector que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Meta Networks Connector.

**Para configurar o logon único do Azure AD com o Meta Networks Connector, execute as seguintes etapas:**

1. No portal do Azure, sobre o **Meta redes conector** página de integração do aplicativo, clique em **logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

1. Sobre o **Meta redes conector do domínio e URLs** , realize as seguintes etapas se desejar configurar o aplicativo no **IDP** modo iniciado pelo:

    ![Meta Networks Connector Domain e informações de conexão única de URLs](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    1. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`
    
    1. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`
    
1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Meta Networks Connector Domain e informações de conexão única de URLs](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`
    
    1. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador real, o URL de resposta e o URL de logon explicados posteriormente no tutorial.
    
1. O aplicativo Meta Networks Connector espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)
    
1. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo | NAMESPACE|
    | ---------------| --------------- | -------- |
    | nome | user.givenname | |
    | sobrenome | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Nome | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    1. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

        ![Configurar o logon único](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)
    
        ![Configurar o logon único](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)   
    
    1. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    1. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    1. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.
    
    1. Clique em **Ok**
    
1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.
    
    ![O link de download do Certificado](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)
    
1. Na seção **Configuração do conector Meta Networks**, clique em **Configurar conector Meta Networks** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)
    
1. Clique no botão **Salvar** .
    
    ![Botão Salvar em Configurar Logon Único](./media/metanetworksconnector-tutorial/tutorial_general_400.png)
    
1. Abra uma nova guia no seu navegador e faça login na sua conta de administrador do Meta Networks Connector.
    
    > [!NOTE]
    > O Meta Networks Connector é um sistema seguro. Portanto, antes de acessar o portal precisa obter sua lista de permissões de endereços IP pública em seu lado. Para obter seu endereço IP público, siga o link especificado abaixo [aqui](https://whatismyipaddress.com/). Enviar seu endereço IP para o [equipe de suporte do cliente de conector de redes de Meta](mailto:support@metanetworks.com) para obter seu endereço IP na lista de permissões.
    
1. Vá para **Administrator** e selecione **configurações**.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure3.png)
    
1. Certifique-se **Log de tráfego da Internet** e **força VPN MFA** estiver definida como off.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure1.png)
    
1. Vá para **Administrator** e selecione **SAML**.
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure4.png)
    
1. Execute as seguintes etapas na **detalhes** página:
    
    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure2.png)
    
    1. Copie o valor de **SSO URL** e cole-o na caixa de texto **URL de login** na seção **Domínio do conector de Meta Networks e URLs**.
    
    1. Copie **o URL do destinatário** e cole-o na caixa de texto **URL de resposta** na seção **Domínio e URLs do conector do Meta Networks**.
    
    1. Cópia **URI de audiência (ID da entidade SP)** de valor e cole-o na **identificador (ID da entidade)** caixa de texto o **Meta redes conector do domínio e URLs da** seção.
    
    1. Habilitar o SAML
    
1. Sobre o **geral** guia execute as seguintes etapas:

    ![Configurar o logon único](./media/metanetworksconnector-tutorial/configure5.png)

    1. No **URL de Logon Único do Provedor de Identidade**, cole o **URL do Serviço de Logon Único do SAML** que você copiou do portal do Azure.

    1. No **emissor do provedor de identidade**, cole o **ID da entidade SAML** valor que você copiou do portal do Azure.

    1. Abra o certificado baixado do portal do Azure no bloco de notas, cole-o na **certificado x. 509** caixa de texto.

    1. Habilitar o **provisionamento Just-in-Time**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.
    
![Criar um usuário de teste do Azure AD][100]
    
**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.
    
    ![O botão Azure Active Directory](./media/metanetworksconnector-tutorial/create_aaduser_01.png)
    
1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/metanetworksconnector-tutorial/create_aaduser_02.png)
    
1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.
    
    ![O botão Adicionar](./media/metanetworksconnector-tutorial/create_aaduser_03.png)
    
1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/metanetworksconnector-tutorial/create_aaduser_04.png)
    
    1. Na caixa **Nome**, digite **BrendaFernandes**.

    1. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.
    
    1. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.
    
    1. Clique em **Criar**.
    
### <a name="create-a-meta-networks-connector-test-user"></a>Criar um usuário de teste do conector de redes de Meta

O objetivo desta seção é criar um usuário chamado Britta Simon no Meta Networks Connector. O Meta Networks Connector oferece suporte ao provisionamento just-in-time, que é ativado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar Meta redes conector se ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a  [equipe de suporte ao Cliente do Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao Meta Networks Connector.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon a Meta redes conector, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.
    
    ![Atribuir usuário][201]
    
1. Na lista de aplicativos, selecione **Meta redes conector**.
    
    ![O link de Meta redes conector na lista de aplicativos](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  
    
1. No menu à esquerda, clique em **usuários e grupos**.
    
    ![O link “Usuários e grupos”][202]
    
1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.
    
    ![O painel Adicionar Atribuição][203]
    
1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.
    
1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.
    
1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Meta Networks Connector no Painel de Acesso, deve se conectar automaticamente ao seu aplicativo Meta Networks Connector.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

