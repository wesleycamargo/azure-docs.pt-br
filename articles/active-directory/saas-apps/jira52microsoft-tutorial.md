---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do SAML para o JIRA da Microsoft (V5.2) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o JIRA da Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 7ea727d5fb004ca224407a8b1c93a6f11448b5e7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231348"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integração do Azure Active Directory ao SSO do SAML para o JIRA da Microsoft (V5.2)

Neste tutorial, você aprenderá a integrar o SSO do JIRA SAML da Microsoft (V5.2) ao Azure AD (Azure Active Directory).

A integração do SSO do SAML para o JIRA da Microsoft (V5.2) ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SSO do SAML para o JIRA da Microsoft (V5.2).
- É possível permitir que os usuários se conectem automaticamente ao SSO do SAML para o JIRA da Microsoft (V5.2) (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>DESCRIÇÃO

Use sua conta do Microsoft Azure Active Directory com o servidor do Atlassian JIRA para habilitar o logon único. Dessa forma, todos os usuários de sua organização podem usar as credenciais do Azure AD para fazer logon no aplicativo JIRA. Este plug-in usa o SAML 2.0 para federação.

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao SSO do SAML para o JIRA da Microsoft (V5.2), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- O JIRA Core e o Software 5.2 devem estar instalados e configurados na versão de 64 bits do Windows
- O servidor do JIRA é habilitado para HTTPS
- Observe que as versões com suporte no Plug-in do JIRA são mencionadas na seção abaixo.
- O servidor do JIRA é acessível pela Internet, especialmente na página de Logon do Azure AD para autenticação e deve conseguir receber o token do Azure AD
- As credenciais do administrador são configuradas no JIRA
- O WebSudo está desabilitado no JIRA
- Usuário de teste criado no aplicativo para servidores do JIRA

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção do JIRA. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

**Versões com suporte:**

*   JIRA Core e Software: 5.2
*   O JIRA também é compatível com o 6.0 e o 7.8. Para obter mais detalhes, clique em [SSO do SAML para o JIRA da Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SSO do SAML para o JIRA da Microsoft (V5.2) por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Adicionando o SSO do SAML para o JIRA da Microsoft (V5.2) por meio da galeria
Para configurar a integração do JIRA SAML SSO pela Microsoft (V5.2) ao Azure AD, você precisa adicionar o JIRA SAML SSO da Microsoft (V5.2) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SSO do SAML para o JIRA da Microsoft (V5.2) por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SSO do SAML para o JIRA da Microsoft (V5.2)**, selecione **SSO do SAML para o JIRA da Microsoft (V5.2)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SSO do SAML para o JIRA da Microsoft (V5.2) na lista de resultados](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o JIRA SAML SSO da Microsoft (V5.2) com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SSO do SAML para o JIRA da Microsoft (V5.2) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para o JIRA da Microsoft (V5.2).

Para configurar e testar o logon único do Azure AD com o SSO do SAML para o JIRA da Microsoft (V5.2), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[ Crie um SSO do JIRA SAML pelo usuário de teste da Microsoft (V5.2) ](#create-a-jira-saml-sso-by-microsoft-v52-test-user)** - para ter um equivalente de Brenda Fernandes no JIRA SAML SSO da Microsoft (V5.2) que esteja vinculado à Representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SSO do SAML para o JIRA da Microsoft (V5.2).

**Para configurar o logon único do Azure AD com o SSO do SAML para o JIRA da Microsoft (V5.2), realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **SSO do SAML para o JIRA da Microsoft (V5.2)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. Na seção **Domínio e URLs do SSO do SAML para o JIRA da Microsoft**, realize as seguintes etapas:

    ![Informações de logon único e domínio do SSO do SAML para o JIRA da Microsoft](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in do Jira, que é explicada adiante no tutorial.

4. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![Configurar o logon único](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. Em outra janela do navegador da Web, faça logon na instância do JIRA como administrador.

7. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Configurar o logon único](./media/jira52microsoft-tutorial/addon1.png)

8. Na seção da guia Complementos, clique em **Gerenciar complementos**.

    ![Configurar o logon único](./media/jira52microsoft-tutorial/addon7.png)

9. Baixe o plug-in no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Carregar manualmente o plug-in fornecido pela Microsoft usando o menu **Carregar complemento**. O download do plug-in está coberto pelo [Contrato de Serviço da Microsoft](https://www.microsoft.com/en-us/servicesagreement/).

    ![Configurar o logon único](./media/jira52microsoft-tutorial/addon12.png)

10. Depois que o plug-in for instalado, ele aparecerá na seção de complementos **Instalado pelo Usuário**. Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar o logon único](./media/jira52microsoft-tutorial/addon13.png)

11. Realize as seguintes etapas na página de configuração:

    ![Configurar o logon único](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo, para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, após a resolução dos metadados, o administrador receberá um erro.

    a. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique no botão **Resolver**. Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

    b. Copie os valores de **Identificador, URL de Resposta e URL de Logon** e cole-os nas caixas de texto **Identificador, URL de Resposta e URL de Logon**, respectivamente, na seção **Domínio e URLs do SSO do SAML para o JIRA da Microsoft (V5.2)** do portal do Azure.

    c. Em **Nome do Botão de Logon**, digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.

    d. Em **Locais da ID de Usuário SAML**, selecione **A ID de Usuário está no elemento NameIdentifier da instrução Subject** ou **A ID de Usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário do JIRA. Se a ID de usuário não tiver uma correspondência, o sistema não permitirá que os usuários façam logon.

    > [!Note]
    > O local padrão da ID de Usuário SAML é o Identificador de Nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.

    e. Se você selecionar a opção **A ID de Usuário está em um elemento Attribute**, na caixa de texto **Nome do atributo**, digite o nome do atributo em que a ID de Usuário é esperada. 

    f. Se estiver usando o domínio federado (como o ADFS, etc.) com o Azure AD, clique na opção **Habilitar Descoberta de Realm Inicial** e configure o **Nome de Domínio**.

    g. Em **Nome de Domínio**, digite o nome de domínio aqui, no caso do logon baseado no ADFS.

    h. Marque a opção **Habilitar Logoff Único** se desejar fazer logoff do Azure AD quando um usuário fizer logoff do JIRA. 

    i. Clique no botão **Salvar** para salvar as alterações.

    > [!NOTE]
    > Para obter mais informações sobre instalação e solução de problemas, visite o [Guia do Administrador do Conector de SSO para MS JIRA](../ms-confluence-jira-plugin-adminguide.md), e há também as [perguntas frequentes](../ms-confluence-jira-plugin-faq.md) para auxiliá-lo

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Criar um usuário de teste do SSO do SAML para o JIRA da Microsoft (V5.2)

Para permitir que os usuários do Azure AD façam logon no servidor local do JIRA, eles devem ser provisionados no servidor local do JIRA.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no servidor local do JIRA como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Adicionar Funcionário](./media/jira52microsoft-tutorial/user1.png)

3. Você será redirecionado à página de acesso de administrador para inserir a **Senha** e clicar no botão **Confirmar**.

    ![Adicionar Funcionário](./media/jira52microsoft-tutorial/user2.png)

4. Na seção da guia **Gerenciamento de usuário**, clique em **criar usuário**.

    ![Adicionar Funcionário](./media/jira52microsoft-tutorial/user3.png) 

5. Na página da caixa de diálogo **"Criar novo usuário"**, execute as seguintes etapas:

    ![Adicionar Funcionário](./media/jira52microsoft-tutorial/user4.png)

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao JIRA SAML SSO pela Microsoft (V5.2).

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao SSO do SAML para o JIRA da Microsoft (V5.2), realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SSO do SAML para o JIRA da Microsoft (V5.2)**.

    ![Link do SSO do SAML para o JIRA da Microsoft (V5.2) na lista Aplicativos](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco JIRA SAML SSO by Microsoft (V5.2) no Painel de Acesso, deve entrar automaticamente no seu aplicativo JIRA SAML SSO by Microsoft (V5.2).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira5.2-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira5.2-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira5.2-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira5.2-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira5.2-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira5.2-tutorial/tutorial_general_200.png
[201]: ./media/msaadssojira5.2-tutorial/tutorial_general_201.png
[202]: ./media/msaadssojira5.2-tutorial/tutorial_general_202.png
[203]: ./media/msaadssojira5.2-tutorial/tutorial_general_203.png