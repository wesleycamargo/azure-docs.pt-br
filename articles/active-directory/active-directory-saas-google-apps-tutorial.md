---
title: 'Tutorial: Integração do Azure Active Directory com o G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: f785a74d5cab7c2fbfb0e2e1036dfddbb0f4838b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: Integração do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Microsoft Azure AD (Azure Active Directory).

A integração do G Suite ao Microsoft Azure AD oferece os seguintes benefícios:

- No Microsoft Azure AD, você pode controlar quem tem acesso ao G Suite.
- Você pode habilitar seus usuários a fazerem logon automaticamente no G Suite (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do G Suite
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes
1.  **P: Essa integração dá suporte para integração de SSO do Google Cloud Platform com o Microsoft Azure AD?**
    
    R: Sim. O Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração com o GCP, será necessário configurar o SSO com o Google Apps.


2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
   
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o motivo dos usuários receberem solicitações de credenciais duas vezes.

3. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**
   
    A: Sim, dependendo de [qual G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você quer habilitar ou desabilitar para sua organização.

4. **P: Eu posso habilitar logon único apenas para um subconjunto dos meus usuários do G Suite?**
   
    R: Não, ativar o logon único exige que todos os usuários do G Suite autentiquem-se imediatamente com as credenciais do Microsoft Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do ambiente do G Suite pode ser o Microsoft Azure AD ou o Google -- mas não os dois ao mesmo tempo.

5. **P: Se um usuário conectar através do Windows, ele será autenticado automaticamente no G Suite sem ser solicitado a fornecer uma senha?**
   
    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](active-directory-aadconnect-user-signin.md) . Ambas as opções exigem que você execute as etapas do tutorial a seguir para ativar o logon único entre o Microsoft Azure AD e o G Suite.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o G Suite da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-g-suite-from-the-gallery"></a>Adicionar o G Suite da galeria
Para configurar a integração do G Suite ao Microsoft Azure AD, é necessário adicionar o G Suite da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o G Suite da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **G Suite**, selecione **G Suite** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![G Suite na lista de resultados](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o G Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do G Suite é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no G Suite.

No G Suite, atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure AD com o G Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do G Suite](#create-a-g-suite-test-user)** – para ter um equivalente de Brenda Fernandes no G Suite vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no Portal do Azure e configura o logon único no aplicativo G Suite.

**Para configurar o logon único do Microsoft Azure AD com o G Suite, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **G Suite**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. Na seção **Domínio e URLs do G Suite Domain** se você quiser configurar para o **Gmail**, siga estas etapas:

    ![Informações de logon único de Domínio e URLs do G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_urlgmail.png)

    a. Na caixa de texto **URL de Entrara**, digite uma URL usando o padrão a seguir: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do G Suite](https://www.google.com/contact/) para obter esses valores.

4. Na seção **Domínio e URLs do G Suite Domain** se você quiser configurar para o **Google Cloud Platform**, siga estas etapas:

    ![Informações de logon único de Domínio e URLs do G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url1.png)

    a. Na caixa de texto **URL de Entrara**, digite uma URL usando o padrão a seguir: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do G Suite](https://www.google.com/contact/) para obter esses valores. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do G Suite**, clique em **Configurar o G Suite** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a URL do Serviço de Logon Único SAML e a URL de Alteração de Senha** da **seção Referência Rápida.**

    ![Configurar o G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

8. Abra uma nova guia no navegador e entre no [Console de Admin do G Suite](http://admin.google.com/) usando sua conta de administrador.

9. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.
   
    ![Clique em Segurança.][10]

10. Na página **Segurança**, clique em **Configurar logon único (SSO).**
   
    ![Clique em SSO.][11]

11. Realize as seguintes alterações de configuração:
   
    ![Configure o SSO][12]
   
    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** do G Suite, cole o valor da **URL do Serviço de Logon Único** copiado do Portal do Azure.

    c. No campo **URL da página de saída** do G Suite, cole o valor da **URL de Saída** copiado do Portal do Azure. 

    d. No campo **URL de alteração de senha** do G Suite, cole o valor da **URL de alteração de senha** copiado do Portal do Azure. 

    e. No G Suite, para o **Certificado de verificação**, carregue o certificado baixado do Portal do Azure.

    f. Selecione **Usar um emissor de domínio específico**.

    g. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-g-suite-test-user"></a>Crie um usuário de teste do G Suite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no G Suite Software. O G Suite dá suporte ao provisionamento automático, que é habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no G Suite Software, um novo será criado quando você tentar acessar o G Suite Software.

>[!NOTE] 
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao G Suite.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao G Suite, execute as etapas a seguir:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do G Suite no Painel de Acesso, deverá fazer logon automaticamente no aplicativo do G Suite.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

