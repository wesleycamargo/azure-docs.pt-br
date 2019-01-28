---
title: 'Tutorial: Integração do Azure Active Directory ao Cisco Spark | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: jeedes
ms.openlocfilehash: e5ed2abca6c9a8bab6ee0df7a8aa05e40b48de5e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810798"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: Integração do Azure Active Directory ao Cisco Spark

Neste tutorial, você aprenderá como integrar o Cisco Spark ao Azure AD (Azure Active Directory).
A integração do Cisco Spark ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Cisco Spark.
* Você pode permitir que os usuários sejam conectados automaticamente ao Cisco Spark (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Cisco Spark, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Cisco Spark

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cisco Spark dá suporte ao SSO iniciado por **SP**

## <a name="adding-cisco-spark-from-the-gallery"></a>Adicionando o Cisco Spark da galeria

Para configurar a integração do Cisco Spark ao Azure AD, você precisa adicionar o Cisco Spark da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Spark da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco Spark**, selecione **Cisco Spark** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Cisco Spark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Cisco Spark, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Spark.

Para configurar e testar o logon único do Azure AD com o Cisco Spark, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Cisco Spark](#configure-cisco-spark-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Cisco Spark](#create-cisco-spark-test-user)** – para ter um equivalente de Brenda Fernandes no Cisco Spark que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Cisco Spark, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco Spark**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Cisco Spark](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL como: `https://web.ciscospark.com/#/signin`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://idbroker.webex.com/<companyname>`

    > [!NOTE]
    > O valor de Identificador não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao Cliente do Cisco Spark](https://support.ciscospark.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Cisco Spark espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | NOME |  Atributo de Origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Cisco Spark**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-cisco-spark-single-sign-on"></a>Configurar o Logon Único do Cisco Spark

1. Entre no [Gerenciamento de Colaboração de Nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais completas de administrador.

2. Escolha **Configurações** e, na seção **Autenticação**, clique em **Modificar**.

    ![Configurar o logon único](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Escolha **Integrar um provedor de identidade de terceiros. (Avançado)** e vá para a próxima tela.

4. Na página **Importar Metadados Idp**, arrastre e solte o arquivo de metadados do Azure AD na página ou use a opção de navegador de arquivos para localizar e carregar o arquivo de metadados do Azure AD. Em seguida, escolha **Exigir certificado assinado por uma autoridade de certificação em Metadados (mais seguro)** e clique em **Avançar**.

    ![Configurar o logon único](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Escolha **Testar Conexão SSO** e, quando uma nova guia do navegador for aberta, autentique-se com o Azure AD conectando-se.

6. Volte para a guia do navegador **Gerenciamento de Colaboração de Nuvem da Cisco**. Se o teste tiver sido bem-sucedido, escolha a opção **Este teste foi executado com êxito. Habilitar Logon Único** e clique em **Avançar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Cisco Spark.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Cisco Spark**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Cisco Spark**.

    ![O link do Cisco Spark na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cisco-spark-test-user"></a>Criar um usuário de teste do Cisco Spark

Nesta seção, você criará um usuário chamado Brenda Fernandes no Cisco Spark. Nesta seção, você criará um usuário chamado Brenda Fernandes no Cisco Spark.

1. Acesse o [Gerenciamento de Colaboração de Nuvem da Cisco](https://admin.ciscospark.com/) com suas credenciais completas de administrador.

2. Clique em **Usuários** e em **Gerenciar Usuários**.
   
    ![Configurar o logon único](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Na janela **Gerenciar Usuário**, escolha **Adicionar ou modificar usuários manualmente** e clique em **Avançar**.

4. Escolha **Nomes e Endereço de email**. Em seguida, preencha a caixa de texto como se segue:

    ![Configurar o logon único](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário como **britta.simon@contoso.com**.

5. Clique no sinal de mais para adicionar Brenda Fernandes. Em seguida, clique em **Avançar**.

6. Na janela **Adicionar Serviços para Usuários**, clique em **Salvar** e em **Concluir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cisco Spark no Painel de Acesso, você deverá ser conectado automaticamente ao Cisco Spark, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)