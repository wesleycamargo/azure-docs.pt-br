---
title: 'Tutorial: Integração do Azure Active Directory com o CloudSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Infor CloudSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 025a9d7df16b2be5e22104010be057403ce53c25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60272806"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Tutorial: Integração do Azure Active Directory com o Infor CloudSuite

Neste tutorial, você aprenderá a integrar o Infor CloudSuite ao Azure Active Directory (Azure AD).

A integração do Infor CloudSuite ao Azure Active Directory fornece a você os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Infor CloudSuite.
- Você pode habilitar seus usuários a fazerem logon automaticamente no Infor CloudSuite (logon único) com suas contas Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao CloudSuite, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Assinatura habilitada para logon único do Infor CloudSuite

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Infor CloudSuite da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>Adição do Infor CloudSuite da Galeria

Para configurar a integração do Infor CloudSuite ao Azure AD, você precisará adicionar o Infor CloudSuite da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Infor CloudSuite a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Infor CloudSuite**, selecione **Infor CloudSuite** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infor CloudSuite na lista de resultados](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Infor CloudSuite, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Infor in the CloudSuite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no Infor CloudSuite.

Para configurar e testar o logon único do AD do Azure com o Infor CloudSuite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando o usuário de teste do Infor CloudSuite](#creating-infor-cloudsuite-test-user)** - para ter um equivalente de Brenda Fernandes no Infor CloudSuite que é vinculado à representação de usuário no Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo portal CloudSuite.

**Para configurar o logon único do Azure Active Directory com o CloudSuite, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Infor CloudSuite**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial-general-301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de URLs e Domínio do Infor CloudSuite](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url1.png)

     a. Na caixa de texto **Identificador**, digite uma URL:
    
    | | |
    |-|-|
    | `http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. Na caixa de texto **URL de Resposta**, digite uma URL:

    | | |
    |-|-|
    | `https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e Domínio do Infor CloudSuite](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`| 

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Infor CloudSuite](mailto:support@infor.com) para obter esse valor.

6. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-certificate.png)

7. Para configurar o logon único no **Infor CloudSuite**, você precisa enviar o **XML de metadados de federação** para [equipe de suporte do Infor CloudSuite](mailto:support@infor.com) baixado. Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-infor-cloudsuite-test-user"></a>Criando um usuário de teste do Infor CloudSuite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Infor CloudSuite. O Infor CloudSuite dá suporte a just-in-time de provisionamento que pode ser habilitado pelo administrador do locatário. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Infor CloudSuite, caso ele ainda não exista.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a  [Equipe de suporte Infor CloudSuite](mailto:support@infor.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Infor CloudSuite.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Infor CloudSuite**.

    ![Configurar o logon único](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Infor CloudSuite no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Infor CloudSuite.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
