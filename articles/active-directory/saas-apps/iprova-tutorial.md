---
title: 'Tutorial: Integração do Azure Active Directory ao iProva | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6576366df214304bb30c46fa65a4bc8bc783cedf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182095"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Tutorial: Integração do Azure Active Directory ao iProva

Neste tutorial, você aprenderá a integrar o iProva ao Azure AD (Azure Active Directory).
A integração do iProva com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao iProva.
* Você pode permitir que os usuários sejam conectados automaticamente ao iProva (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iProva, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês no site do [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para SSO iProva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste:

* O iProva dá suporte ao SSO iniciado por SP.

## <a name="add-iprova-from-the-gallery"></a>Adicionar o iProva da galeria

Para configurar a integração do iProva ao Azure AD, adicione o iProva à lista de aplicativos SaaS gerenciados por meio da galeria.

Para adicionar o iProva da galeria, siga estas etapas:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

    ![A folha Aplicativos Empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **iProva**. Selecione **iProva** do painel de resultados e selecione **Adicionar** para adicionar o aplicativo.

     ![iProva na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iProva, com base em um usuário de teste chamado Brenda Fernandes.
Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iProva.

Para configurar e testar o logon único do Azure AD com o iProva, você precisará concluir os seguintes blocos de construção:

- [Recuperar informações de configuração do iProva](#retrieve-configuration-information-from-iprova) – como uma preparação para as próximas etapas.
- [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
- [Configurar o Logon Único do iProva](#configure-iprova-single-sign-on) – para definir as configurações de Logon Único no lado do aplicativo.
- [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
- [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
- [Criar um usuário de teste do iProva](#create-an-iprova-test-user) – para ter um equivalente de Brenda Fernandes no iProva que esteja vinculado à representação de usuário no Azure AD.
- [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="retrieve-configuration-information-from-iprova"></a>Recuperar informações de configuração do iProva

Nesta seção, você pode recuperar informações de iProva para configurar o logon único do Azure AD.

1. Abra um navegador da Web e vá para a **página de informações do SAML2** no iProva, usando o seguinte padrão de URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Exibir a página de informações do SAML2 no iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Deixe a guia do navegador aberta enquanto você continua com as próximas etapas em outra guia do navegador.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com iProva, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iProva**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Ícone Editar na configuração básica do SAML](common/edit-urls.png)

4. Na seção **Configuração SAML Básica**, siga estas etapas.

     a. Preencha a caixa **Identificador** com o valor exibido por trás do rótulo **EntityID** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    b. Preencha a caixa **URL de resposta** com o valor exibido por trás do rótulo **URL de resposta** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    c. Preencha a caixa **URL de Logon** com o valor exibido por trás do rótulo **URL de Logon** na página **Informações de SAML2 do iProva**. Esta página ainda está aberta em outra guia do navegador.

    ![Informações de logon único de domínio e URLs do iProva](common/sp-identifier-reply.png)

5. O aplicativo iProva espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. Na página **Definir o logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![A caixa de diálogo Atributos do Usuário](common/edit-attribute.png)

6. Na seção **Declarações do Usuário**, na caixa de diálogo **Atributos do Usuário**, configure o atributo do token SAML como mostrado na imagem anterior. Siga estas etapas.

    | NOME | Atributo de origem| Namespace |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

     a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Declarações de usuário](common/new-save-attribute.png)

    ![Gerenciar a caixa de diálogo de declarações do usuário](common/new-attribute-details.png)

    b. Na caixa **Nome**, insira o nome do atributo mostrado para aquela linha.

    c. Na lista **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Selecione a opção **Fonte** como **Atributo**.

    e. Na lista **Atributo de origem**, insira o valor do atributo mostrado para essa linha.

    f. Selecione **Ok**.

    g. Clique em **Salvar**.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o ícone **Copiar** para a **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurar logon único do iProva

1. Entre no iProva usando a conta **Administrador**.

2. Abra o menu **Ir para**.

3. Selecione **Gerenciamento de aplicativos**.

4. Selecione **Geral** no painel **Configurações do sistema**.

5. Selecione **Editar**.

6. Role a tela para baixo até **Controle de acesso**.

    ![Configurações de controle de acesso do iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Encontre a configuração **Os usuários são conectados automaticamente com suas contas de rede** e altere-a para **Sim, autenticação via SAML**. Opções adicionais aparecem agora.

8. Selecione **Configurar**.

9. Selecione **Avançar**.

10. O iProva pergunta se você deseja baixar os dados de federação de uma URL ou carregá-los de um arquivo. Selecione a opção **Da URL**.

    ![Baixar metadados do Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Cole a URL de metadados salva na última etapa da seção "Configurar o logon único do Azure AD".

12. Selecione o botão em forma de seta para baixar os metadados do Azure AD.

13. Quando o download for concluído, a mensagem de confirmação **Arquivo de Dados de Federação válido baixado** será exibida.

14. Selecione **Avançar**.

15. Ignore a opção **Testar logon** por enquanto e, em seguida, selecione **Avançar**.

16. Na caixa suspensa **Declaração para uso**, selecione **windowsaccountname**.

17. Selecione **Concluir**.

18. Agora você retornará para a tela **Editar configurações gerais**. Role para baixo até a parte inferior da página e, em seguida, selecione **OK** para salvar sua configuração.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![Os links Usuários e grupos e Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, siga estas etapas.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. Na caixa **Nome**, insira um nome como **BrendaFernandes**.
  
    b. Na caixa **Nome de usuário**, insira *yourname@yourcompanydomain.extension*. 
    Um exemplo é BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao iProva.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **iProva**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iProva**.

    ![O link do iProva na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![A caixa de diálogo Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e, em seguida, escolha **Selecionar** na parte inferior da tela.

6. Se você esperar qualquer valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Escolha **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-an-iprova-test-user"></a>Criar um usuário de teste do iProva

1. Entre no iProva usando a conta **Administrador**.

2. Abra o menu **Ir para**.

3. Selecione **Gerenciamento de aplicativos**.

4. Clique em **Usuários** no painel **Usuários e grupos de usuários**.

5. Selecione **Adicionar**.

6. Na caixa **Nome de usuário**, insira *brittasimon@yourcompanydomain.extension*. 
    Um exemplo é BrittaSimon@contoso.com.

7. Na caixa **Nome completo**, insira um nome completo, como **BrendaFernandes**.

8. Selecione a opção **Nenhuma senha (usar logon único)**.

9. No **Endereço de email**, insira *yourname@yourcompanydomain.extension*. 
   Um exemplo é BrittaSimon@contoso.com.

10. Role para baixo até o final da página e, em seguida, selecione **Concluir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do iProva no Painel de Acesso, você deverá ser conectado automaticamente ao iProva, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva – Como configurar o logon único do SAML2](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
