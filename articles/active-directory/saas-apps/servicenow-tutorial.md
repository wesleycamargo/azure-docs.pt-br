---
title: 'Tutorial: Integração do Azure Active Directory ao ServiceNow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 470805b2bb77e367887767b95e0f1e04d79c8f9d
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830728"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Active Directory do Azure com o ServiceNow

Neste tutorial, você aprenderá como integrar o ServiceNow ao Azure Active Directory (Azure AD).

A integração do Service Now ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ServiceNow.
- Você pode permitir que seus usuários façam logon automaticamente no ServiceNow (Logon Único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ServiceNow, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Para o ServiceNow, uma instância ou um locatário do ServiceNow, versão Calgary ou superior
- Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinki ou superior
- O locatário ServiceNow deve ter o [Plug-in de Logon Único de Provedor Múltiplo](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito [enviando uma solicitação de serviço](https://hi.service-now.com).
- Para configuração automática, habilite o plug-in do multi-provedor para ServiceNow.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ServiceNow da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da galeria

Para configurar a integração do ServiceNow no Azure AD, você precisa adicionar o ServiceNow da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ServiceNow da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ServiceNow**, selecione **ServiceNow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ServiceNow na lista de resultados](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o ServiceNow, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ServiceNow é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.

No ServiceNow, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ServiceNow, você precisará concluir os seguintes blocos de construção:

1. **[Configurar Logon Único do Azure AD para o ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** - para permitir que seus usuários usem esse recurso.
2. **[Configurar Logon Único do Azure AD para o ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** - para permitir que seus usuários usem esse recurso.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Criar um usuário de teste do ServiceNow](#create-a-servicenow-test-user)** - para ter um equivalente de Brenda Fernandes no ServiceNow que esteja vinculado à representação dela no Azure AD.
5. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurar Logon Único do Azure AD para o ServiceNow

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo ServiceNow.

**Para configurar o logon único do Azure AD com o ServiceNow, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **ServiceNow**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Clique em **Alterar modo de Logon único** na parte superior da tela para selecionar o modo **SAML**.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_300.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_301.png)

4. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_302.png)

5. Na seção **Configuração básica de SAML**, execute as etapas a seguir:

    ![Informações de logon único de Domínio e URLs do ServiceNow](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esses valores com o Identificador e a URL de Entrada reais que são explicados mais adiante no tutorial.

6. Na seção **Certificado de Autenticação SAML**, execute as seguintes etapas:

    ![O link de download do Certificado](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Clique no botão copiar para copiar a **URL de Metadados de Federação de Aplicativos** e cole-a no bloco de notas, pois essa URL de metadados de federação de aplicativos será usada posteriormente no tutorial.

    b. Clique em **Download** para baixar 0 **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

7. Entre no seu aplicativo ServiceNow como administrador.

8. Ative o plug-in **Integração - Instalador de Logon Único de Vários Provedores** ao seguir as próximas etapas:

    a. No painel de navegação à esquerda, procure a seção **Definição do Sistema** na barra de pesquisa e, em seguida, clique em **Plug-ins**.

    ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar o plug-in")

     b. Procure **Integração - Instalador de Logon Único de Vários Provedores**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar o plug-in")

    c. Selecione o plugin. Clique com o botão direito do mouse e selecione **Ativar/Atualizar**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate.png "Ativar o plug-in")

    d. Clique no botão **Ativar**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate1.png "Ativar o plug-in")

9. No painel de navegação à esquerda, procure a seção **SSO de Vários Provedores** na barra de pesquisa e, em seguida, clique em **Propriedades**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do Aplicativo")

10. No diálogo **Várias propriedades de SSO do provedor** , execute as seguintes etapas:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981.png "Configurar URL do Aplicativo")

    * Como **Habilitar vários provedores SSO**, selecione **Sim**.
  
    * Para **Habilitar importação automática de usuários de todos os provedores de identidade na tabela de usuário**, selecione **Sim**.

    * Para **Habilitar log de depuração para integração SSO de vários provedores**, selecione **Sim**.

    * Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.
  
    * Clique em **Salvar**.

11. Há dois modos como **ServiceNow** pode ser configurado - Automático e Manual.

12. Para configurar o **ServiceNow** automaticamente, siga as etapas a seguir:

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
  
13. Para configurar o **ServiceNow** manualmente, siga as etapas a seguir:

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

1. No portal do Azure, na página de integração do aplicativo **ServiceNow**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Clique em **Alterar modo de Logon único** na parte superior da tela para selecionar o modo **SAML**.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_300.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_301.png)

4. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_302.png)

5. Na seção **Configuração básica de SAML**, execute as etapas a seguir:

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente ServiceNow](https://www.servicenow.com/support/contact-support.html) para obter esses valores.

6. Na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

7. Um serviço de configuração de um clique é fornecido para o ServiceNow, ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configurar ServiceNow**, clique em **Exibir instruções passo a passo** para abrir a janela Configurar logon.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

8. Insira o nome da instância ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar o logon** e clique em **Configurar agora**. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar o ServiceNow manualmente para usar o Azure AD como um Provedor de Identidade SAML, clique em **Configurar o logon único manualmente** e copie a **URL de Logon, ID de Entidade do SAML e a URL do Serviço de Logon Único do SAML** da seção Referência Rápida.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do Aplicativo")

9. Entre no seu aplicativo ServiceNow Express como administrador.

10. No painel de navegação à esquerda, clique em **Logon Único**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do Aplicativo")

11. No diálogo **Logon Único**, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do Aplicativo")

    a. Ative/desative **Habilitar SSO de vários provedores** à direita.

    b. Ative/desative **Habilitar registro em log de depuração para a integração de SSO de vários provedores** à direita.

    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.

12. No diálogo **Logon Único**, clique em **Adicionar Novo Certificado**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694973ex.png "Configurar logon único")

13. No diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694975.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ativo**.

    c. Para **Formato**, selecione **PEM**.

    d. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    e. Abra seu certificado codificado em base64 baixado do portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado PEM**.

    f. Clique em **Atualizar**

14. No diálogo **Logon Único**, clique em **Adicionar novo IdP**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694976ex.png "Configurar logon único")

15. No diálogo **Adicionar Novo Provedor de Identidade**, em **Configurar Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694982ex.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **SAML2.0**).

    b. No campo **URL do Provedor de Identidade**, cole o valor da **ID do Provedor de Identidade** que você copiou no portal do Azure.

    c. No campo **AuthnRequest do Provedor de Identidade**, cole o valor da **URL da Solicitação de Autenticação** que você copiou no portal do Azure.

    d. No campo **SingleLogoutRequest do Provedor de Identidade**, cole o valor da **URL de Serviço de Logout Único** que você copiou no portal do Azure

    e. Como **Certificado de Provedor de Identidade**, selecione o certificado que você criou na etapa anterior.

16. Clique em **Configurações Avançadas** e, em **Propriedades Adicionais do Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694983ex.png "Configurar logon único")

    a. Na caixa de texto **associação de protocolo SingleLogoutRequest do IDP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Na caixa de texto **Política da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. No **Método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Desmarque **Criar um AuthnContextClass**.

17. Em **Propriedades Adicionais do Provedor de Serviço**, execute as seguintes etapas:

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

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/servicenow-tutorial/create_aaduser_01.png) 

3. Nas propriedades do Usuário, execute as seguintes etapas.

    ![Criação de um usuário de teste do AD do Azure](./media/servicenow-tutorial/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário** digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="create-a-servicenow-test-user"></a>Criar um usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ServiceNow. O ServiceNow dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar [aqui](servicenow-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

> [!NOTE]
> Se for necessário criar um usuário manualmente, você precisará entrar em contato com a [Equipe de suporte do cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ServiceNow.

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de Aplicativos](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ServiceNow no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo ServiceNow.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](servicenow-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
