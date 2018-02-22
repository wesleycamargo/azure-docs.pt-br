---
title: "Tutorial: integração do Azure Active Directory com o Adobe Experience Manager | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Experience Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c366e314b77cd3344a90826b22b96a45e35b0b4e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: integração do Azure Active Directory com o Adobe Experience Manager

Neste tutorial, você aprenderá como integrar o Adobe Experience Manager ao Azure AD (Azure Active Directory).

A integração do Azure Experience Manager ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Adobe Experience Manager.
- É possível permitir que os usuários entrem automaticamente no Adobe Experience Manager com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o Adobe Experience Manager, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Adobe Experience Manager

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, poderá [obter uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adobe Experience Manager da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Adicionar o Adobe Experience Manager da galeria
Para configurar a integração do Adobe Experience Manager ao Azure AD, será necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Experience Manager da galeria, siga as etapas a seguir:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Adobe Experience Manager**. Selecione **Adobe Experience Manager** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Experience Manager na lista de resultados](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Experience Manager, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário no Adobe Experience Manager é equivalente a um usuário do Azure AD. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no Adobe Experience Manager.

No Adobe Experience Manager, dê ao valor **Username** o mesmo valor do **nome de usuário** no Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários. 

Para configurar e testar o logon único do Azure AD com o Adobe Experience Manager, conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) para ter um equivalente de Brenda Fernandes no Adobe Experience Manager vinculado à representação do usuário do Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Adobe Experience Manager.

**Para configurar o logon único do Azure AD com o Adobe Experience Manager, siga estas etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Adobe Experience Manager**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, no menu suspenso **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Na seção **Domínio e URLs do Adobe Experience Manager**, siga as etapas a seguir se desejar configurar o aplicativo no modo **IdP**:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Na caixa de texto **Identificador**, digite um valor exclusivo também definido no servidor do AEM. 

    b. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Para obter esses valores, contate a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Marque **Mostrar configurações de URL avançadas**. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Informações de logon único sobre Domínio e URLs do Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Na caixa **URL de logon**, digite a URL de servidor do Adobe Experience Manager. 

5. Na seção **Certificado de Autenticação SAML**, selecione **Certificados (Base64)**. Em seguida, salve o arquivo de certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Na seção Configuração do Adobe Experience Manager, selecione **Configurar Adobe Experience Manager** para abrir a janela Configurar logon. Copie a **URL do Serviço de Logon do SAML**, a **ID da Entidade SAML** e a **ID de saída** da seção Referência Rápida.

    ![Link da seção de configuração](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Selecione **Salvar**.

    ![Configurar o botão salvar do logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Abra o portal de administração do **Adobe Experience Manager** em outra janela do navegador.

9. Selecione **Configurações** > **Segurança** > **Usuários**.

    ![Configurar o botão salvar do logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Selecione **Administrador** ou qualquer outro usuário relevante.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Selecione **Configurações da conta** > **Gerenciar TrustStore**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Em **Adicionar Certificado do arquivo CER**, clique no botão **Selecionar Arquivo de Certificado**. Navegue até o arquivo de certificado que você baixou do Portal do Azure e selecione-o.

    ![Configurar o botão salvar do logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. O certificado é adicionado ao TrustStore. Observe o alias do certificado.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na página **Usuários**, selecione **authentication-service**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Selecione **Configurações de conta** > **Criar/gerenciar repositório de chaves**. Criar repositório de chaves fornecendo uma senha.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Volte para a tela de administrador. Em seguida, selecione **Configurações** > **Operações** > **Console Web**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Isso abre a página de configuração.

    ![Configurar o botão salvar do logon único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Localize **Manipulador de autenticação do Adobe Granite SAML 2.0**. Selecione então o ícone **Adicionar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Siga as ações a seguir nesta página.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa **Caminho**, digite **/**.

    b. Na caixa **URL de IDP**, insira o valor da **URL de Serviço de Logon SAML** que você copiou do Portal do Azure.

    c. No **Alias do certificado IDP**, digite o valor do **Alias do Certificado** que você adicionou na TrustStore.

    d. Na caixa **ID da Entidade Fornecida de Segurança**, insira o valor exclusivo da **ID da Entidade SAML** que você configurou no Portal do Azure.

    e. Na caixa **URL do Serviço do Consumidor de Declaração**, digite o valor da **URL de Resposta** que você configurou no Portal do Azure.

    f. Na caixa **Senha do Repositório de Chaves**, digite a **Senha** que você definiu no repositório de chaves.

    g. Na caixa **ID do Atributo de Usuário**, insira a **ID do Nome** ou outra ID de usuário relevante em seu caso.

    h. Selecione **Criar Usuários CRX Automaticamente**.

    i. Na caixa **URL de Logoff**, insira o valor da **URL de Logoff** que você copiou do Portal do Azure.

    j. Selecione **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com) enquanto você estiver configurando o aplicativo. Depois de adicionar este aplicativo por meio da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único**. Em seguida, acesse a documentação inserida por meio da seção **Configuração** na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, na parte superior da caixa de diálogo **Todos os Usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Selecione a caixa de seleção **Mostrar Senha**. Em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Criar um usuário de teste do Adobe Experience Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Adobe Experience Manager. Se você selecionar a opção **Criar Usuários CRX Automaticamente**, os usuários serão criados automaticamente após a autenticação bem-sucedida. 

Se você desejar criar usuários manualmente, trabalhe com a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os usuários na plataforma do Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará para Brenda Fernandes o uso do logon único do Azure, concedendo a ela o acesso ao Adobe Experience Manager.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Adobe Experience Manager, siga estas etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Em seguida, vá até a exibição de diretório, selecione **Aplicativos empresariais** e depois **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Adobe Experience Manager**.

    ![O link do Adobe Experience Manager na lista de aplicativos](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco do Adobe Experience Manager no painel de acesso, você deverá entrar automaticamente em seu aplicativo Adobe Experience Manager.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
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

