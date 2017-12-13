---
title: "Tutorial: integração do Azure Active Directory com o Adobe Experience Manager | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Experience Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4ba94f0f24b2791b3b32807aa60379aeadb79365
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: integração do Azure Active Directory com o Adobe Experience Manager

Neste tutorial, você aprenderá como integrar o Adobe Experience Manager ao Azure AD (Azure Active Directory).

A integração do Azure Experience Manager ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Adobe Experience Manager.
- É possível permitir que os usuários entrem automaticamente no Adobe Experience Manager (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Experience Manager, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Adobe Experience Manager

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adobe Experience Manager da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionando o Adobe Experience Manager da galeria
Para configurar a integração do Adobe Experience Manager ao Azure AD, será necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Experience Manager da galeria, siga as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Adobe Experience Manager**, selecione **Adobe Experience Manager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Experience Manager na lista de resultados](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Experience Manager, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário no Adobe Experience Manager é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Adobe Experience Manager.

No Adobe Experience Manager, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Adobe Experience Manager, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Adobe Experience Manager](#create-an-adobe-experience-manager-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Experience Manager vinculado à representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Adobe Experience Manager.

**Para configurar o logon único do Azure AD com o Adobe Experience Manager, siga estas etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Adobe Experience Manager**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Na seção **Domínio e URLs do Adobe Experience Manager**, siga as etapas a seguir se desejar configurar o aplicativo no modo **IdP**:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Na caixa de texto **Identificador**, digite um valor exclusivo definido no servidor AEM também. 

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para obter esses valores.
 
4. Marque Mostrar configurações avançadas de URL e siga a etapa a seguir se desejar configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Na caixa de texto **URL de logon**, digite a URL de servidor do Adobe Experience Manager. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Na seção Configuração do Adobe Experience Manager, clique em Configurar Adobe Experience Manager para abrir a janela Configurar logon. Copie a **URL do Serviço de Logon do SAML**, a **ID da Entidade SAML** e a **ID de saída** da seção Referência rápida.

    ![Link da Seção de configuração](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Abra o portal de administração do **Adobe Experience Manager** em outra janela do navegador.

9. Selecione **Configurações** -> **Segurança** -> **Usuários**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Selecione **Administrador** ou qualquer outro usuário relevante.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Selecione **Configurações de conta** -> **Criar/gerenciar TrustStore**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Clique em **Selecionar arquivo de certificado** no botão **Adicionar certificado do arquivo CER**. Procure e selecione o arquivo de certificado, que você baixou do Portal do Azure.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. O certificado é adicionado ao TrustStore. Observe o alias do certificado.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na página **Usuários**, selecione **authentication-service**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Selecione **Configurações de conta** -> **Criar/gerenciar repositório de chaves**. Criar repositório de chaves fornecendo uma senha.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Volte para a tela de administração e selecione **Configurações** -> **Operações** -> **Console Web**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. Isso abre a página Configuração.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. Localize **Manipulador de autenticação do Adobe Granite SAML 2.0** e clique no ícone **Adicionar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Siga estas ações nesta página.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa de texto **Caminho**, digite **/**.

    b. Na caixa de texto **URL do IDP**, insira o valor da **URL do Serviço de Logon SAML**, copiado do Portal do Azure.

    c. Na caixa de texto **Alias do certificado IDP**, insira o valor do **Alias do certificado** que você adicionou no TrustStore.

    d. Na caixa de texto **ID da entidade fornecida de segurança**, insira o valor da **ID da Entidade SAML** exclusiva, configurada no Portal do Azure.

    e. Na caixa de texto **URL do Serviço do Consumidor de Declaração**, insira o valor da **URL de Resposta**, configurado no Portal do Azure.

    f. Na caixa de texto **Senha do repositório de chaves**, digite a **Senha**, definida no repositório de chaves.

    g. Na caixa de texto **ID do atributo de usuário**, insira a **ID do Nome** ou outra ID de Usuário relevante em seu caso.

    h. Selecione **Criar usuários CRX automaticamente.**

    i. Na caixa de texto **URL de Logoff**, insira o valor da **URL de Saída** que você tem do Portal do Azure.

    j. Clique em **Salvar**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Criar um usuário de teste do Adobe Experience Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Adobe Experience Manager. Se você tiver selecionado a opção **Criar usuários CRX automaticamente**, então os usuários serão criados automaticamente após a autenticação bem-sucedida. 

Se você desejar criar usuários manualmente, trabalhe com a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os usuários na plataforma do Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Adobe Experience Manager.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Adobe Experience Manager, siga estas etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Adobe Experience Manager**.

    ![O link do Adobe Experience Manager na lista de aplicativos](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Experience Manager no Painel de Acesso, você deverá entrar automaticamente em seu aplicativo Adobe Experience Manager.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

