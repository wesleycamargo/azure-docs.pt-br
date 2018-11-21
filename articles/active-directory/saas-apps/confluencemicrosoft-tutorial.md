---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do SAML para o Confluence da Microsoft | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o Confluence da Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeedes
ms.openlocfilehash: 2e254faae0289cd00c7e66d430ec3148fccb364a
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288145"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Integração do Azure Active Directory ao SSO do SAML para o Confluence da Microsoft

Neste tutorial, você aprende a integrar o SSO do SAML para o Confluence da Microsoft ao Azure AD (Azure Active Directory).

A integração do SSO do SAML para o Confluence da Microsoft ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Confluence SAML SSO da Microsoft.
- Você pode permitir que seus usuários façam logon automaticamente no Confluence SAML SSO da Microsoft (Single Sign-On) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Descrição:

Use sua conta do Microsoft Azure Active Directory com o servidor do Atlassian Confluence para habilitar o logon único. Dessa forma, todos os usuários de sua organização podem usar as credenciais do Azure AD para fazer logon no aplicativo Confluence. Este plug-in usa o SAML 2.0 para federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do SAML para o Confluence da Microsoft, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Aplicativo para servidores do Confluence instalado em um servidor do Windows de 64 bits (localmente ou na infraestrutura de IaaS na nuvem)
- O servidor do Confluence é habilitado para HTTPS
- Observe que as versões com suporte no Plug-in do Confluence são mencionadas na seção abaixo.
- O servidor do Confluence é acessível pela Internet, especialmente na página de Logon do Azure AD para autenticação e deve conseguir receber o token do Azure AD
- As credenciais do administrador são configuradas no Confluence
- O WebSudo está desabilitado no Confluence
- Usuário de teste criado no aplicativo para servidores do Confluence

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção do Confluence. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Versões com suporte do Confluence

A partir de agora, há suporte para as seguintes versões do Confluence:

- Confluence: 5.0 a 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SSO do SAML para o Confluence da Microsoft por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Adicionando o SSO do SAML para o Confluence da Microsoft por meio da galeria

Para configurar a integração do SSO do SAML para o Confluence da Microsoft ao Azure AD, é necessário adicionar o SSO do SAML para o Confluence da Microsoft à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o SSO do SAML para o Confluence da Microsoft por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Confluence SAML SSO da Microsoft**, selecione **Confluence SAML SSO pela Microsoft** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![SSO do SAML para confluence pela Microsoft na lista de resultados](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO do SAML para o Confluence da Microsoft, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SSO do SAML para o Confluence da Microsoft é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para o Confluence da Microsoft.

Para configurar e testar o logon único do Azure AD com o SSO do SAML para o Confluence da Microsoft, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[ Criando Confluence SAML SSO pelo usuário de teste da Microsoft](#creating-confluence-saml-sso-by-microsoft-test-user)** - para ter um equivalente de Brenda Fernandes no SSO SAML do Confluence da Microsoft que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SSO do SAML para o Confluence da Microsoft.

**Para configurar o logon único do Azure AD com o SSO do SAML para o Confluence da Microsoft, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **SSO do SAML para o Confluence da Microsoft**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Confluence SAML SSO por domínio da Microsoft e informações de logon único de URLs](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in do Confluence, que é explicada adiante no tutorial.

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique no botão Copiar para copiar  **URL de metadados da federação de aplicativo**  e cole-o no Bloco de notas.

    ![O link de download do Certificado](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)

6. Em outra janela do navegador da Web, faça logon na instância do Confluence como administrador.

7. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Configurar o logon único](./media/confluencemicrosoft-tutorial/addon1.png)

8. Baixe o plug-in no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Carregar manualmente o plug-in fornecido pela Microsoft usando o menu **Carregar complemento**. O download do plug-in está coberto pelo [Contrato de Serviço da Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar o logon único](./media/confluencemicrosoft-tutorial/addon12.png)

9. Depois que o plug-in for instalado, ele será exibido na seção de complementos **Instalados pelo Usuário** da seção **Gerenciar Complemento**. Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar o logon único](./media/confluencemicrosoft-tutorial/addon13.png)

10. Realize as seguintes etapas na página de configuração:

    ![Configurar o logon único](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo, para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, após a resolução dos metadados, o administrador receberá um erro.

     a. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique no botão **Resolver**. Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

    b. Copie os valores de **Identificador, URL de Resposta e URL de Logon** e cole-os nas caixas de texto **Identificador, URL de Resposta e URL de Logon**, respectivamente, na seção **Domínio e URLs do SSO do SAML para o Confluence da Microsoft** do portal do Azure.

    c. Em **Nome do Botão de Logon**, digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.

    d. Em **Locais da ID de Usuário SAML**, selecione **A ID de Usuário está no elemento NameIdentifier da instrução Subject** ou **A ID de Usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário do Confluence. Se a ID de usuário não tiver uma correspondência, o sistema não permitirá que os usuários façam logon. 

    > [!Note]
    > O local padrão da ID de Usuário SAML é o Identificador de Nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.
    
    e. Se você selecionar a opção **A ID de Usuário está em um elemento Attribute**, na caixa de texto **Nome do atributo**, digite o nome do atributo em que a ID de Usuário é esperada. 

    f. Se estiver usando o domínio federado (como o ADFS, etc.) com o Azure AD, clique na opção **Habilitar Descoberta de Realm Inicial** e configure o **Nome de Domínio**.
    
    g. Em **Nome de Domínio**, digite o nome de domínio aqui, no caso do logon baseado no ADFS.

    h. Marque a opção **Habilitar Logoff Único** se desejar fazer logoff do Azure AD quando um usuário fizer logoff do Confluence. 

    i. Clique no botão **Salvar** para salvar as alterações.

    > [!NOTE]
    > Para obter mais informações sobre instalação e solução de problemas, visite o [Guia do Administrador do Conector de SSO para MS Confluence](../ms-confluence-jira-plugin-adminguide.md) e há também as [perguntas frequentes](../ms-confluence-jira-plugin-faq.md) para auxiliá-lo

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-confluence-saml-sso-by-microsoft-test-user"></a>Creating Confluence SAML SSO by Microsoft test user

Para permitir que os usuários do Azure AD façam logon no servidor local do Confluence, eles devem ser provisionados no SSO do SAML para o Confluence da Microsoft. Para o SSO do SAML para o Confluence da Microsoft, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no servidor local do Confluence como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Adicionar Funcionário](./media/confluencemicrosoft-tutorial/user1.png) 

3. Na seção usuários, clique na guia **Adicionar usuários**. Na página da caixa de diálogo **Adicionar um Usuário**, execute as seguintes etapas:

    ![Adicionar Funcionário](./media/confluencemicrosoft-tutorial/user2.png) 

     a. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brenda Fernandes.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha de Brenda Fernandes.

    e. Clique em **Confirmar Senha** e redigite a senha.

    f. Clique no botão **Adicionar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SSO do SAML para o Confluence da Microsoft.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SSO do SAML para o Confluence da Microsoft**.

    ![Configurar o logon único](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do SSO do SAML para o Confluence da Microsoft no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo SSO do SAML para o Confluence da Microsoft.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
