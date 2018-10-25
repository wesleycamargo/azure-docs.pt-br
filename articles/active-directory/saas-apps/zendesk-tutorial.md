---
title: 'Tutorial: Integração do Azure Active Directory com o Zendesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268167"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Active Directory do Azure com o Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory).

A integração do Zendesk ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Zendesk.
- Você pode permitir que seus usuários façam logon automaticamente no Zendesk (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zendesk, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Zendesk

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Zendesk da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria

Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zendesk por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Zendesk**, selecione **Zendesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zendesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zendesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Zendesk.

No Zendesk, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Zendesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Zendesk](#create-a-zendesk-test-user)** – para ter um equivalente de Brenda Fernandes no Zendesk vinculado à representação de um usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zendesk.

**Para configurar o logon único do Azure AD com o Zendesk, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Zendesk**, selecione **Logon único**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Na seção **Configuração básica de SAML**, execute as etapas a seguir:

    a. Na caixa de texto **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`.

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de atendimento ao cliente do Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores.

6. O Zendesk espera que as declarações SAML estejam em um formato específico. Não há nenhum atributo SAML obrigatório, mas, opcionalmente, você pode adicionar um atributo da seção **Atributos de Usuário** na página de integração do aplicativo. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Selecione Origem como **Atributo**.
    
    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para a linha.
    
    f. Clique em **Ok**

    g. Clique em **Salvar**.

    > [!NOTE]
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita.

8. Na seção Certificado de Assinatura de SAML, na seção **Certificado de Assinatura de SAML**, copie a **Impressão digital** e salve-a no computador.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Selecione a opção adequada para **Opção de Assinatura**, se necessário.

    b. Selecione a opção adequada para **Algoritmo de Assinatura**, se necessário.

    c. Clique em **Salvar**

9. Na seção **Configurar o Zendesk**, clique em **Exibir instruções passo a passo** para abrir a janela **Configurar logon**. Copie as URLs abaixo da **seção Referência Rápida.**

    Observe que a url pode dizer o seguinte:

    a. URL do serviço de logon único SAML

    b. ID da Entidade

    c. URL de saída

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Há duas maneiras pelas quais o Zendesk pode ser configurado: Automática e Manual.
  
11. Para automatizar a configuração no Zendesk, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. Depois de adicionar a extensão ao navegador, clique em **Instalação do Zendesk**. Você será direcionado para o aplicativo Zendesk. Em seguida, forneça as credenciais de administrador para entrar no Zendesk. A extensão do navegador configurará automaticamente o aplicativo e automatizará a etapa 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Se quiser configurar o Zendesk manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do Zendesk como administrador e execute as seguintes etapas:

    * Clique em **Administrador**.

    * No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.

    * Na página **Segurança**, realize as seguintes etapas:

      ![Segurança](././media/zendesk-tutorial/ic773089.png "Segurança")

      ![Logon Único](././media/zendesk-tutorial/ic773090.png "Logon Único")

      a. Clique na guia **Administrador e Agentes**.

      b. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.

      c. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

      d. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Saída** que você copiou do Portal do Azure.

      e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.

      f. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="create-a-zendesk-test-user"></a>Criar um usuário de teste do Zendesk

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Zendesk. O Zendesk suporta o provisionamento automático de usuários, que é ativado por padrão. Você pode encontrar mais detalhes [ aqui ](Zendesk-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar um usuário manualmente, siga estas etapas:**

> [!NOTE]
> As contas de **Usuário final** são provisionadas automaticamente ao entrar. Contas de **Agente** e **Admin** precisam ser provisionadas manualmente no **Zendesk** antes de entrar.

1. Faça logon em seu locatário do **Zendesk** .

2. Selecione a guia **Lista de Clientes** .

3. Selecione a guia **Usuário** e clique em **Adicionar**.

    ![Adicionar Usuário](././media/zendesk-tutorial/ic773632.png "Adicionar Usuário")
4. Digite o **Nome** e **Email** de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.

    ![Novo Usuário](././media/zendesk-tutorial/ic773633.png "Novo Usuário")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zendesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zendesk no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Zendesk.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](zendesk-provisioning-tutorial.md)
