---
title: 'Tutorial: Integração do Azure Active Directory ao HubSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.openlocfilehash: 2806288378e5fa080164155b97a47a7046ecbba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814895"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Azure Active Directory ao HubSpot

Neste tutorial, você aprenderá a integrar o HubSpot ao Azure AD (Azure Active Directory).

A integração do HubSpot ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao HubSpot
- Você pode permitir que usuários façam logon automaticamente no HubSpot (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HubSpot, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do HubSpot habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do HubSpot por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-hubspot-from-the-gallery"></a>Adição do HubSpot por meio da galeria

Para configurar a integração do HubSpot ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HubSpot por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

4. Na caixa de pesquisa, digite **HubSpot**. Selecione **HubSpot** no painel de resultados e clique no botão para **Adicionar** o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o HubSpot, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do HubSpot é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no HubSpot.

Para configurar e testar o logon único do Azure AD com o HubSpot, você precisa concluir os seguintes passos básicos:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do HubSpot](#creating-a-hubspot-saml-test-user)**: para ter um equivalente de Brenda Fernandes no HubSpot que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo do HubSpot.

**Para configurar o logon único do Azure AD com o HubSpot, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **HubSpot**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_general_301.png)

3. Se você precisar alterar para o modo **SAML** de qualquer outro modo, clique em **Alterar modo de logon único** na parte superior da tela.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_general_300.png)

4. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_general_302.png)

5. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações sobre logon único de domínio e URLs do HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url.png)

     a. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Na caixa de texto **URL de resposta**, digite a URL com o seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e o Identificador reais, que são explicados adiante no tutorial. Contate a [equipe de suporte do Cliente do HubSpot](https://help.hubspot.com/) para obter esses valores.

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações sobre logon único de domínio e URLs do HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.hubspot.com/login`

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Na seção **Configurar HubSpot**, clique no botão Copiar para copiar os valores da **URL de logon** e do **Identificador do Azure AD**.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Abra uma nova guia no navegador e faça logon em sua conta de administrador do HubSpot.

9. Clique no **ícone de configurações** no canto superior direito da página.

    ![Configurar o logon único](./media/hubspot-tutorial/config1.png)

10. Clique em **Padrões da Conta**.

    ![Configurar o logon único](./media/hubspot-tutorial/config2.png)

11. Role para baixo até a seção **Segurança** e clique em **Configurar**.

    ![Configurar o logon único](./media/hubspot-tutorial/config3.png)

12. Na seção **Configurações de logon único**, execute as seguintes etapas:

    ![Configurar o logon único](./media/hubspot-tutorial/config4.png)

     a. Clique no botão **Cópia** para copiar o valor **URl de audiência (ID da entidade do provedor de serviço)** e cole-o na caixa de texto **Identificador** na seção **Configuração básica de SAML** no Portal do Azure.

    b. Clique no botão **}copiar** para copiar o valor **URl, ACS, Destinatário ou Redirecionamento de Logon** e cole-o na caixa de texto **URL de resposta** na seção **Configuração básica de SAML** no Portal do Azure.

    c. Na caixa de texto **Identificador do Provedor de Identidade ou URL do Emissor**, cole o valor do **Identificador do Azure AD**, que você copiou do Portal do Azure.

    d. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do Portal do Azure.

    e. Abra o arquivo **Certificate(Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa **Certificado X.509**.

    f. Clique em **Verificar**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/hubspot-tutorial/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](./media/hubspot-tutorial/create_aaduser_02.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-hubspot-test-user"></a>Criar um usuário de teste do HubSpot

Para permitir que os usuários do Azure AD façam logon no HubSpot, eles devem ser provisionados no HubSpot.
No caso do HubSpot, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa da **HubSpot** como administrador.

2. Clique no **ícone de configurações** no canto superior direito da página.

    ![Configurar o logon único](./media/hubspot-tutorial/config1.png)

3. Clique em **Usuários e Equipes**.

    ![Configurar o logon único](./media/hubspot-tutorial/user1.png)

4. Clique em **Criar usuário**.

    ![Configurar o logon único](./media/hubspot-tutorial/user2.png)

5. Insira o endereço de email do usuário como **brittasimon@contoso.com** na caixa de texto **Adicionar endereços de email** e clique em **Avançar**.

    ![Configurar o logon único](./media/hubspot-tutorial/user3.png)

6. Na seção **Criar usuários**, analise cada guia individual, selecione as opções e as permissões apropriadas para o usuário e clique em **Avançar**.

    ![Configurar o logon único](./media/hubspot-tutorial/user4.png)

7. Clique em **Enviar** para enviar o convite para o usuário.

    ![Configurar o logon único](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O usuário será ativado depois de aceitar o convite.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso à HubSpot.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **HubSpot**.

    ![Configurar o logon único](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco HubSpot no Painel de Acesso, você deve ser conectado automaticamente à página de logon do aplicativo do HubSpot.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png