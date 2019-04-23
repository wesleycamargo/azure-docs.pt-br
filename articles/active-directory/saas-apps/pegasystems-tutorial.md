---
title: 'Tutorial: Integração do Azure Active Directory ao Pega Systems | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 34fe5d85829d909989513214406ba96ea5be0aa8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271094"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Azure Active Directory ao Pega Systems

Neste tutorial, você aprenderá a integrar o Pega Systems ao Azure Active Directory (Azure AD).
A integração do Pega Systems ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Pega Systems.
* Você pode permitir que os usuários sejam conectados automaticamente ao Pega Systems (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Pega Systems, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Pega Systems

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Pega Systems é compatível com SSO iniciado por **SP** e **IDP**

## <a name="adding-pega-systems-from-the-gallery"></a>Adição do Pega Systems da galeria

Para configurar a integração do Pega Systems ao Azure AD, você precisa adicionar o Pega Systems da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Pega Systems da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Pega Systems**, selecione **Pega Systems** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Pega Systems na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Pega Systems com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Pega Systems.

Para configurar e testar o logon único do Azure AD com o Pega Systems, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Pega Systems](#configure-pega-systems-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Pega Systems](#create-pega-systems-test-user)** – para ter um equivalente de Brenda Fernandes no Pega Systems que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Pega Systems, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Pega Systems**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Pega Systems](common/idp-intiated.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Pega Systems](common/both-advanced-urls.png)

     a. Na caixa de texto **URL de entrada**, digite o logon no valor de URL.

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Entrada e a URL de Estado de Retransmissão reais. Você pode encontrar os valores do Identificador e a URL de Resposta do aplicativo Pega que é explicado mais adiante neste tutorial. Para o Estado de Retransmissão, contate a [equipe de suporte ao Cliente do Pega Systems](https://www.pega.com/contact-us) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Pega Systems espera as declarações do SAML em um formato específico, o que exige adicionar mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo Pega Systems espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | ------------------- | -------------------- |
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefone | *********** |

    > [!NOTE]
    > Esses são valores específicos do cliente. Forneça os valores apropriados.

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o Pega Systems**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-pega-systems-single-sign-on"></a>Configurar logon único do Pega Systems

1. Para configurar o logon único no **Pega Systems**, abra o **Portal do Pega** com a conta do administrador em outra janela do navegador.

2. Selecione **Criar** -> **SysAdmin** -> **Serviço de Autenticação**.

    ![Botão Salvar em Configurar Logon Único](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Execute as seguintes ações na tela **Criar Serviço de Autenticação**:

    ![Botão Salvar em Configurar Logon Único](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

     a. Selecione **SAML 2.0** em Tipo

    b. Na caixa de texto **Nome**, insira qualquer nome, por exemplo, SSO do Azure AD

    c. Na caixa de texto **Descrição curta**, insira qualquer descrição  

    d. Clique em **Criar e abrir** 
    
4. Na seção **Informações do IdP (Provedor de Identidade)**, clique em **Importar metadados do IdP** e procure o arquivo de metadados do qual foi feito o download no portal do Azure. Clique em **Enviar** para carregar os metadados.

    ![Botão Salvar em Configurar Logon Único](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. Isso populará os dados do IdP, conforme mostrado abaixo.

    ![Botão Salvar em Configurar Logon Único](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Execute as seguintes ações na seção **Configurações do SP (Provedor de Serviços)**:

    ![Botão Salvar em Configurar Logon Único](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

     a. Copie o valor **Identificação da Entidade** e cole-o na caixa de texto **Identificador** na **Configuração Básica de SAML** no portal do Azure.

    b. Copie o valor **Local do ACS (Serviço do Consumidor de Declaração)** e cole-o na caixa de texto **URL de Resposta** na **Configuração Básica de SAML** no portal do Azure.

    c. Selecione **Desabilitar assinatura de solicitação**.

7. Clique em **Salvar**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Pega Systems.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Pega Systems**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Pega Systems**.

    ![O link do Pega Systems na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-pega-systems-test-user"></a>Criar usuário de teste do Pega Systems

O objetivo desta seção é criar uma usuária chamado Brenda Fernandes no Pega Systems. Trabalhe com a [equipe de suporte ao Cliente do Pega Systems](https://www.pega.com/contact-us) para criar usuários no Pega Systems.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Pega Systems no Painel de Acesso, você deverá ser conectado automaticamente ao Pega Systems para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)