---
title: 'Tutorial: Integração do Azure Active Directory ao ServiceNow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: b7201f2a5d2e1b3967bd84ddb78797debdf14aeb
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065691"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Azure Active Directory com o ServiceNow

Neste tutorial, você aprenderá como integrar o ServiceNow ao Azure Active Directory (Azure AD).
A integração do Service Now ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ServiceNow.
* Você pode permitir que seus usuários entrem automaticamente no ServiceNow (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ServiceNow, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do ServiceNow habilitada para logon único
* Para o ServiceNow, uma instância ou um locatário do ServiceNow, versão Calgary ou superior
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinki ou superior
* O locatário ServiceNow deve ter o [Plug-in de Logon Único de Provedor Múltiplo](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito [enviando uma solicitação de serviço](https://hi.service-now.com).
* Para configuração automática, habilite o plug-in do multi-provedor para ServiceNow.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ServiceNow dá suporte a SSO iniciado por **SP**

* O ServiceNow dá suporte ao [provisionamento de usuário **Automatizado**](servicenow-provisioning-tutorial.md)

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da galeria

Para configurar a integração do ServiceNow no Azure AD, você precisa adicionar o ServiceNow da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ServiceNow da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ServiceNow**, selecione **ServiceNow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ServiceNow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o ServiceNow, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ServiceNow.

Para configurar e testar o logon único do Azure AD com o ServiceNow, você precisará concluir os seguintes blocos de construção:

1. **[Configurar Logon Único do Azure AD para o ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** - para permitir que seus usuários usem esse recurso.
2. **[Configurar o logon único do ServiceNow](#configure-servicenow-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Configurar Logon Único do Azure AD para o ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** - para permitir que seus usuários usem esse recurso.
4. **[Configurar o logon único do ServiceNow Express](#configure-servicenow-express-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
5. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
6. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
7. **[Criar um usuário de teste do ServiceNow](#create-servicenow-test-user)** – para ter um equivalente de Brenda Fernandes no ServiceNow que esteja vinculado à representação dela no Azure AD.
8. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurar Logon Único do Azure AD para o ServiceNow

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ServiceNow, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **ServiceNow**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ServiceNow](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esses valores com o Identificador e a URL de Entrada reais que são explicados mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

     a. Clique no botão copiar para copiar a **URL de Metadados de Federação de Aplicativos** e cole-a no bloco de notas, pois essa URL de metadados de federação de aplicativos será usada posteriormente no tutorial.

    b. Clique em **Download** para baixar 0 **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

6. Na seção **Configurar o ServiceNow**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

     a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-servicenow-single-sign-on"></a>Configurar o logon único do ServiceNow

1. Entre no seu aplicativo ServiceNow como administrador.

2. Ative o plug-in **Integração - Instalador de Logon Único de Vários Provedores** ao seguir as próximas etapas:

     a. No painel de navegação à esquerda, procure a seção **Definição do Sistema** na barra de pesquisa e, em seguida, clique em **Plug-ins**.

    ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar o plug-in")

    b. Procure **Integração - Instalador de Logon Único de Vários Provedores**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar o plug-in")

    c. Selecione o plugin. Clique com o botão direito do mouse e selecione **Ativar/Atualizar**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate.png "Ativar o plug-in")

    d. Clique no botão **Ativar**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate1.png "Ativar o plug-in")

3. No painel de navegação à esquerda, procure a seção **SSO de Vários Provedores** na barra de pesquisa e, em seguida, clique em **Propriedades**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do Aplicativo")

4. No diálogo **Várias propriedades de SSO do provedor** , execute as seguintes etapas:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981.png "Configurar URL do Aplicativo")

    * Como **Habilitar vários provedores SSO**, selecione **Sim**.
  
    * Para **Habilitar importação automática de usuários de todos os provedores de identidade na tabela de usuário**, selecione **Sim**.

    * Para **Habilitar log de depuração para integração SSO de vários provedores**, selecione **Sim**.

    * Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.
  
    * Clique em **Salvar**.

5. Há dois modos como **ServiceNow** pode ser configurado - Automático e Manual.

6. Para configurar o **ServiceNow** automaticamente, siga as etapas a seguir:

    * Volte para a página de Logon Único do **ServiceNow** no portal do Azure.

    * Um serviço de configuração de um clique é fornecido para o ServiceNow, ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configuração do ServiceNow**, clique em **Configurar ServiceNow** para abrir a janela de logon Configurar.

        ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Insira o nome da instância ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar o logon** e clique em **Configurar agora**. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar o ServiceNow manualmente para usar o Azure AD como um Provedor de Identidade SAML, clique em **Configurar o logon único manualmente** e copie a **URL de Logon, ID de Entidade do SAML e a URL do Serviço de Logon Único do SAML** da seção Referência Rápida.

        ![Configurar URL do Aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do Aplicativo")

    * Entre no seu aplicativo ServiceNow como administrador.

    * Na configuração automática, todas as configurações necessárias são configuradas no lado do **ServiceNow**, mas o **Certificado X.509** não é habilitado por padrão. É necessário mapeá-lo manualmente para o Provedor de Identidade no ServiceNow. Siga as etapas abaixo para o mesmo:

    * No painel de navegação esquerdo, pesquise a seção **SSO do Multi-Provider** na barra de pesquisa e clique em **Provedores de Identidade**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

    * Clique no Provedor de Identidade gerado automaticamente

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar logon único")

    *  Na seção **Provedor de Identidade**, execute as seguintes etapas:

        ![Configurar logon único](./media/servicenow-tutorial/automatic_config.png "Configurar logon único")

        * Na caixa de texto **Nome**, digite um nome para a configuração (por exemplo, **Logon único Federado do Microsoft Azure**).

        * Remova o valor **SingleLogoutRequest do Provedor de Identidade** da caixa de texto.

        * Copie o valor da **Página inicial do ServiceNow Homepage**, cole-o na caixa de texto de **URL de logon** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

            > [!NOTE]
            > A home page da instância do ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

        * Copie o valor de **Emissor / ID da Entidade**, cole-o na caixa de texto de **Identificador** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

        * Certifique-se de que a **Diretiva NameID** esteja definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

    * Role para baixo até a seção **Certificado X.509**, selecione **Editar**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar logon único")

    * Selecione o certificado e clique no ícone de seta à direita para adicionar o certificado

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar logon único")

    * Clique em **Salvar**.

    * Clique em **Testar conectividade** no canto superior direito da página.

        ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate2.png "Ativar o plug-in")

    * Após clicar em **Testar conectividade**, a janela pop-up em que é necessário inserir as credenciais e a página abaixo com os resultados serão exibidos. O erro **Resultados de Teste de Logout do SSO** é esperado, ignore o erro e clique no botão **Ativar**.

        ![Configurar logon único](./media/servicenow-tutorial/servicenowactivate.png "Configurar logon único")
  
7. Para configurar o **ServiceNow** manualmente, siga as etapas a seguir:

    * Entre no seu aplicativo ServiceNow como administrador.

    * No painel de navegação à esquerda, clique em **Provedores de Identidade**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

    * Na caixa de diálogo **Provedores de Identidade**, clique em **Novo**.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694977.png "Configurar logon único")

    * Na caixa de diálogo **Provedores de Identidade**, clique em **SAML**.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694978.png "Configurar logon único")

    * Na janela pop-up **Importar metadados do Provedor de Identidade**, execute as etapas a seguir:

        ![Configurar logon único](./media/servicenow-tutorial/idp.png "Configurar logon único")

        * Insira a **URL de Metadados de Federação de Aplicativos** que você copiou do Portal do Azure.

        * Clique em **Importar**.

    * Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694982.png "Configurar logon único")

        * Na caixa de texto **Nome**, digite um nome para a configuração (por exemplo, **Logon único Federado do Microsoft Azure**).

        * Remova o valor **SingleLogoutRequest do Provedor de Identidade** da caixa de texto.

        * Copie o valor da **Página inicial do ServiceNow Homepage**, cole-o na caixa de texto de **URL de logon** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

            > [!NOTE]
            > A home page da instância de ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

        * Copie o valor de **Emissor / ID da Entidade**, cole-o na caixa de texto de **Identificador** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

        * Certifique-se de que a **Diretiva NameID** esteja definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Clique em **Avançado**. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

            > [!NOTE]
            > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

        * Clique em **Testar conectividade** no canto superior direito da página.

        * Após clicar em **Testar conectividade**, a janela pop-up em que é necessário inserir as credenciais e a página abaixo com os resultados serão exibidos. O erro **Resultados de Teste de Logout do SSO** é esperado, ignore o erro e clique no botão **Ativar**.

          ![Configurar logon único](./media/servicenow-tutorial/servicenowactivate.png "Configurar logon único")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurar o Logon Único do Azure AD para o ServiceNow Express

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **ServiceNow**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ServiceNow](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esses valores com o Identificador e a URL de Entrada reais que são explicados mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Um serviço de configuração de um clique é fornecido para o ServiceNow, ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configurar ServiceNow**, clique em **Exibir instruções passo a passo** para abrir a janela Configurar logon.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Insira o nome da instância ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar o logon** e clique em **Configurar agora**. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar o ServiceNow manualmente para usar o Azure AD como um Provedor de Identidade SAML, clique em **Configurar o logon único manualmente** e copie a **URL de Logon, ID de Entidade do SAML e a URL do Serviço de Logon Único do SAML** da seção Referência Rápida.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do Aplicativo")

### <a name="configure-servicenow-express-single-sign-on"></a>Configurar o logon único do ServiceNow Express

1. Entre no seu aplicativo ServiceNow Express como administrador.

2. No painel de navegação à esquerda, clique em **Logon Único**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do Aplicativo")

3. No diálogo **Logon Único**, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do Aplicativo")

     a. Ative/desative **Habilitar SSO de vários provedores** à direita.

    b. Ative/desative **Habilitar registro em log de depuração para a integração de SSO de vários provedores** à direita.

    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.

4. No diálogo **Logon Único**, clique em **Adicionar Novo Certificado**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694973ex.png "Configurar logon único")

5. No diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694975.png "Configurar logon único")

     a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ativo**.

    c. Para **Formato**, selecione **PEM**.

    d. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    e. Abra seu certificado codificado em base64 baixado do portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado PEM**.

    f. Clique em **Atualizar**

6. No diálogo **Logon Único**, clique em **Adicionar novo IdP**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694976ex.png "Configurar logon único")

7. No diálogo **Adicionar Novo Provedor de Identidade**, em **Configurar Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694982ex.png "Configurar logon único")

     a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No campo **URL do Provedor de Identidade**, cole o valor da **ID do Provedor de Identidade** que você copiou no portal do Azure.

    c. No campo **AuthnRequest do Provedor de Identidade**, cole o valor da **URL da Solicitação de Autenticação** que você copiou no portal do Azure.

    d. No campo **SingleLogoutRequest do Provedor de Identidade**, cole o valor da **URL de logoff** que você copiou no portal do Azure

    e. Como **Certificado de Provedor de Identidade**, selecione o certificado que você criou na etapa anterior.

8. Clique em **Configurações Avançadas** e, em **Propriedades Adicionais do Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694983ex.png "Configurar logon único")

     a. Na caixa de texto **associação de protocolo SingleLogoutRequest do IDP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Na caixa de texto **Política da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. No **Método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Desmarque **Criar um AuthnContextClass**.

9. Em **Propriedades Adicionais do Provedor de Serviço**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694984ex.png "Configurar logon único")

     a. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua página inicial de instância do ServiceNow.

    > [!NOTE]
    > A home page da instância de ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Na caixa de texto **ID da entidade/emissor** , digite a URL do seu locatário do ServiceNow.

    c. Na caixa de texto **URI do Público-alvo** , digite a URL do seu locatário do ServiceNow.

    d. Na caixa de texto **Distorção do Relógio**, digite **60**.

    e. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

    f. Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ServiceNow.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ServiceNow**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-servicenow-test-user"></a>Criar um usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ServiceNow. O ServiceNow dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar [aqui](servicenow-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

> [!NOTE]
> Se for necessário criar um usuário manualmente, você precisará entrar em contato com a [Equipe de suporte do cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ServiceNow no Painel de Acesso, você deverá ser conectado automaticamente ao ServiceNow no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](servicenow-provisioning-tutorial.md)