---
title: "Tutorial: Integração do Azure Active Directory com o Google Apps | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Google Apps."
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
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: bdcf2f8a06c782270683e34e9a4cbc2ab2c7f443
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Tutorial: integração do Azure Active Directory ao Google Apps

Neste tutorial, você aprende a integrar o Google Apps ao Azure AD (Azure Active Directory).

A integração do Google Apps ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Google Apps.
- É possível permitir que os usuários se conectem automaticamente ao Google Apps (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Google Apps, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Google Apps

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="video-tutorial"></a>Tutorial em vídeo
Como habilitar o logon único no Google Apps em dois minutos:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>Perguntas frequentes
1. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
   
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Confira este [artigo de suporte do Google Apps](https://support.google.com/chrome/a/answer/6060880) para saber mais sobre o motivo de os usuários receberem uma solicitação por credenciais duas vezes.

2. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**
   
    R: Sim, dependendo de [quais aplicativos do Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você optar por habilitar ou desabilitar para sua organização.

3. **P: Posso habilitar o logon único apenas para um subconjunto de meus usuários do Google Apps?**
   
    R: Não. A ativação do logon único exige imediatamente que todos os usuários do Google Apps se autentiquem com suas credenciais do Azure AD. Como o Google Apps não oferece suporte a vários provedores de identidade, o provedor de identidade para seu ambiente do Google Apps pode ser o AD do Azure ou o Google, mas não ambos ao mesmo tempo.

4. **P: Se um usuário se conectar por meio do Windows, ele será autenticado automaticamente no Google Apps sem que uma senha seja solicitada?**
   
    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](active-directory-aadconnect-user-signin.md) . Ambas as opções exigem que você realize as etapas no tutorial a seguir para habilitar o logon único entre o Azure AD e o Google Apps.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Google Apps por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-google-apps-from-the-gallery"></a>Adicionando o Google Apps por meio da galeria
Para configurar a integração do Google Apps ao Azure AD, é necessário adicionar o Google Apps à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Google Apps por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Google Apps**, selecione **Google Apps** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Google Apps na lista de resultados](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Google Apps, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Google Apps é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Google Apps.

No Google Apps, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Google Apps, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Google Apps](#create-a-google-apps-test-user)** – para ter um equivalente de Brenda Fernandes no Google Apps que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Google Apps.

**Para configurar o logon único do Azure AD com o Google Apps, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Google Apps**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. Na seção **Domínio e URLs do Google Apps**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://mail.google.com/a/<yourdomain.com>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `google.com`|
    | `http://google.com`|
    | `google.com/<yourdomain.com>`|
    | `http://google.com/a/<yourdomain.com>`|
       
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Google Apps](https://www.google.com/contact/) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Google Apps**, clique em **Configurar o Google Apps** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a URL do Serviço de Logon Único SAML e a URL de Alteração de Senha** da **seção Referência Rápida.**

    ![Configuração do Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. Abra uma nova guia no navegador e entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador.

8. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.
   
    ![Clique em Segurança.][10]

9. Na página **Segurança**, clique em **Configurar logon único (SSO).**
   
    ![Clique em SSO.][11]

10. Realize as seguintes alterações de configuração:
   
    ![Configure o SSO][12]
   
    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** do Google Apps, cole o valor do **URL do Serviço de Logon Único** copiado do portal do Azure.

    c. No campo **URL da página de saída** do Google Apps, cole o valor do **URL de Saída** copiado do portal do Azure. 

    d. No campo **URL de Alteração de Senha** do Google Apps, cole o valor de **URL de alteração de senha** copiado do portal do Azure. 

    e. No Google Apps, para o **Certificado de verificação**, carregue o certificado baixado no portal do Azure.

    f. Clique em **Salvar Alterações**.

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
 
### <a name="create-a-google-apps-test-user"></a>Criar um usuário de teste do Google Apps

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Google Apps Software. O Google Apps dá suporte ao provisionamento automático, que está habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Google Apps Software, um novo será criado quando você tentar acessar o Google Apps Software.

>[!NOTE] 
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Google Apps.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Google Apps, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Google Apps**.

    ![O link Google Apps na lista de aplicativos](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Google Apps no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Google Apps.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

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

