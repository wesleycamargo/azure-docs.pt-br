---
title: 'Tutorial: Integração do Azure Active Directory com Ivanti Service Manager (ISM) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.openlocfilehash: 7a125ea70a891d8db0ec652844a99153f19253bd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150284"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Tutorial: Integração do Azure Active Directory com Ivanti Service Manager (ISM)

Neste tutorial, você aprenderá como integrar o Invanti Service Manager (ISM) ao Azure Active Directory (Azure AD).

A integração do Ivanti Service Manager (ISM) ao Azure AD fornece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Ivanti Service Manager (ISM).
- É possível permitir que os usuários entrem automaticamente no Ivanti Service Manager (ISM) (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Ivanti Service Manager (ISM), são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Ivanti Service Manager (ISM)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Ivanti Service Manager (ISM) a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Adicionando o Ivanti Service Manager (ISM) a partir da galeria

Para configurar a integração do Ivanti Service Manager (ISM) ao Azure AD, será necessário adicionar Ivanti Service Manager (ISM) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Ivanti Service Manager (ISM) da galeria, siga as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Ivanti Service Manager (ISM)**, selecione **Ivanti Service Manager (ISM)** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Ivanti Service Manager (ISM) na lista de resultados](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com Ivanti Service Manager (ISM) com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário no Ivanti Service Manager (ISM) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Ivanti Service Manager (ISM) precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Ivanti Service Manager (ISM), é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Ivanti Service Manager (ISM)](#creating-an-ivanti-service-manager-ism-test-user)** para ter um equivalente de Brenda Fernandes Ivanti Service Manager (ISM) vinculado à representação do usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Ivanti Service Manager (ISM).

**Para configurar o logon único do Azure AD com o Ivanti Service Manager (ISM), siga estas etapas:**

1. No portal do Azure, na página de integração do aplicativo **Ivanti Service Manager (ISM)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial-general-301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único sobre Domínio e URLs do Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único sobre Domínio e URLs do Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com [equipe de suporte do cliente do Ivanti Service Manager (ISM) ](https://www.ivanti.com/support/contact) para obter esses valores.

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer download do **certificado (Raw)** e salvar o certificado arquivo no seu computador.

    ![O link de download do Certificado](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. Na seção **Configuração do Ivanti Service Manager (ISM)**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração Ivanti Service Manager (ISM)](common/configuresection.png)

8. Para configurar o logon único no **Ivanti Service Manager (ISM)**, é necessário enviar o **Certificado (bruto)** baixado e o **URL de logon** copiado,  **Identificador do Azure AD**, **URL de logoff** à [equipe de suporte do Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>Criando um usuário de teste Ivanti Service Manager (ISM)

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Ivanti Service Manager (ISM). O Ivanti Service Manager (ISM) dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Ivanti Service Manager (ISM), caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a  [equipe de suporte remoto do Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Ivanti Service Manager (ISM).

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Ivanti Service Manager (ISM)**.

    ![Configurar o logon único](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na camada do Ivanti Service Manager (ISM) no Painel de Acesso, você deverá entrar automaticamente em seu aplicativo Ivanti Service Manager (ISM).
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
