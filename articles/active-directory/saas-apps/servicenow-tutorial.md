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
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5d5c4d5e26fa21488dd637805a4c22bd3ed18a7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421076"
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
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ServiceNow da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da galeria
Para configurar a integração do ServiceNow no Azure AD, você precisa adicionar o ServiceNow da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ServiceNow da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **ServiceNow**, selecione **ServiceNow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ServiceNow na lista de resultados](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o ServiceNow, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ServiceNow é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.

No ServiceNow, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ServiceNow, você precisará concluir os seguintes blocos de construção:

1. **[Configurar Logon Único do Azure AD para o ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** - para permitir que seus usuários usem esse recurso.
1. **[Configurar Logon Único do Azure AD para o ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** - para permitir que seus usuários usem esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do ServiceNow](#create-a-servicenow-test-user)** - para ter um equivalente de Brenda Fernandes no ServiceNow que esteja vinculado à representação dela no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurar Logon Único do Azure AD para o ServiceNow

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo ServiceNow.

**Para configurar o logon único do Azure AD com o ServiceNow, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **ServiceNow**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. Na seção **Domínio e URLs do ServiceNow**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ServiceNow](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Esses valores não são reais. Você precisará atualizar esses valores com o Identificador e a URL de Entrada reais que são explicados mais adiante no tutorial.

1. Na seção **Certificado de Autenticação SAML**, execute as seguintes etapas: 

    ![O link de download do Certificado](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Clique no botão copiar para copiar a **URL de Metadados de Federação de Aplicativos** e cole-a no bloco de notas, pois essa URL de metadados de federação de aplicativos será usada posteriormente no tutorial.

    b. Clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/servicenow-tutorial/tutorial_general_400.png)

1. Entre no seu aplicativo ServiceNow como administrador.

1. Ative o plug-in **Integração - Instalador de Logon Único de Vários Provedores** ao seguir as próximas etapas:

    a. No painel de navegação à esquerda, procure a seção **Definição do Sistema** na barra de pesquisa e, em seguida, clique em **Plug-ins**.

    ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar o plug-in")

     b. Procure **Integração - Instalador de Logon Único de Vários Provedores**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar o plug-in")

    c. Selecione o plugin. Clique com o botão direito do mouse e selecione **Ativar/Atualizar**.

    d. Clique no botão **Ativar**.

1. Há duas maneiras em que **ServiceNow** pode ser configurado automático e manual.

1. Para configurar o **ServiceNow** automaticamente siga as etapas a seguir

    a. Volte para a página de Logon Único do **ServiceNow** no portal do Azure.

    b. Um serviço de configuração de um clique é fornecido para o ServiceNow, ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configuração do ServiceNow**, clique em **Configurar ServiceNow** para abrir a janela de logon Configurar.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Insira o nome da instância ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar o logon** e clique em **Configurar agora**. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, clique em **Configurar manualmente o logon único** e copie a **URL de logout, ID de Entidade de SAML e a URL de Serviço de Logon Único do SAML** da seção de Referência rápida.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do Aplicativo")

    d. Entre no seu aplicativo ServiceNow como administrador.

    e. Na configuração automática, todas as configurações necessárias são configuradas no lado do **ServiceNow**, mas o **Certificado X.509** não é habilitado por padrão. Você precisa mapeá-lo manualmente para o provedor de identidade no ServiceNow. Siga as etapas abaixo para o mesmo:
    
    * No painel de navegação à esquerda, clique em **Provedores de Identidade** em **SSO do Multi-provedor**.

      ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

    * Clique no provedor de identidade gerado automaticamente

      ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar logon único")

    * Role para baixo até a seção **Certificado X.509**. Selecione **Editar**.

      ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar logon único")
    
    * Selecione o certificado e clique no ícone de seta à direita para adicionar o certificado

      ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar logon único")

    * Clique em **Salvar**.

    * Clique em **Ativar** no canto superior direito da página.

1. Para configurar o **ServiceNow** manualmente siga as etapas a seguir

1. Entre no seu aplicativo ServiceNow como administrador.

1. No painel de navegação à esquerda, procure a seção **SSO de Vários Provedores** na barra de pesquisa e, em seguida, clique em **Propriedades**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do Aplicativo")

1. No diálogo **Várias propriedades de SSO do provedor** , execute as seguintes etapas:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981.png "Configurar URL do Aplicativo")

    a. Como **Habilitar vários provedores SSO**, selecione **Sim**.

    b. Para **Habilitar importação automática de usuários de todos os provedores de identidade na tabela de usuário**, selecione **Sim**.

    c. Para **Habilitar log de depuração para integração SSO de vários provedores**, selecione **Sim**.

    d. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.

    e. Clique em **Salvar**.

1. No painel de navegação à esquerda, procure a seção **SSO de Vários Provedores** na barra de pesquisa e, em seguida, clique em **Certificados x509**.

    ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_05.png "Configurar logon único")

1. No diálogo **Certificados x. 509**, clique em **Novo**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694974.png "Configurar logon único")

1. No diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694975.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ativo**.

    c. Para **Formato**, selecione **PEM**.

    d. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    e. Abra seu certificado codificado em Base64 baixado do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado PEM**.

     f. Clique em **Enviar**.

1. No painel de navegação à esquerda, clique em **Provedores de Identidade**.

    ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

1. Na caixa de diálogo **Provedores de Identidade**, clique em **Novo**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694977.png "Configurar logon único")

1. Na caixa de diálogo **Provedores de Identidade**, clique em **SAML2 Atualização1?**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694978.png "Configurar logon único")

1. No diálogo Propriedades de SAML2 Atualização1, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/idp.png "Configurar logon único")

    a. Selecione a opção de **URL** na caixa de diálogo **Importar metadados de provedor de identidade**.

    b. Insira a **URL de Metadados de Federação de Aplicativos** que você copiou do Portal do Azure.

    c. Clique em **Importar**.

1. Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694982.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo, **SAML2.0**).
    
    b. Copie o valor da **Página inicial do ServiceNow Homepage**, cole-o na caixa de texto de **URL de logon** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

    > [!NOTE]
    > A home page da instância do ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    c. Copie o valor de **Emissor / ID da Entidade**, cole-o na caixa de texto de **Identificador** na seção **Domínio e URLs do ServiceNow** no portal do Azure.

    d. Clique em **Avançado**. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

    e. Em **Certificado x509**, liste o certificado que você criou na etapa anterior.

    > [!NOTE]
    > O ServiceNow não permite a ativação do Idp sem clicar no botão de conexão de teste, para substituir o mesmo, siga as etapas a seguir.

1. Clique no ícone do menu do seu novo provedor de identidade que você criou como parte da configuração e da lista selecione **copiar sys_id**

    ![Configurar logon único](./media/servicenow-tutorial/ic7694992.png "Configurar logon único")

1. Na caixa de pesquisa superior esquerda, procure **sys_properties.list** e pressione enter.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694993.png "Configurar logon único")

1. Clique em **Novo**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694994.png "Configurar logon único")

1. Na seção **Propriedade do Sistema**, realize as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694995.png "Configurar logon único")

    a. Insira valor `glide.authenticate.sso.redirect.idp` na caixa de texto nome.

    b. Na caixa de texto **Valor**, cole a cópia do valor sys_id que você copiou nas etapas anteriores.

    c. Selecionar **Privada**.

    d. Clique em **Enviar**.

1. Clique em **Novo**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694994.png "Configurar logon único")

1. Na seção **Propriedade do Sistema**, realize as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694996.png "Configurar logon único")

    a. Insira valor `glide.authenticate.multisso.test.connection.mandatory` na caixa de texto nome.

    b. Na caixa de texto **Valor**, digite **false**.

    c. Clique em **Enviar**.

1. Depois de concluir a etapa acima, agora você poderá ativar o novo provedor de identidade e o SSO deve funcionar

> [!NOTE]
> Também observe que, você precisa testar a nova configuração de Idp em uma nova janela incógnita

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurar o Logon Único do Azure AD para o ServiceNow Express

1. No portal do Azure, na página de integração do aplicativo **ServiceNow**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. Na seção **Domínio e URLs do ServiceNow**, execute as seguintes etapas:

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente ServiceNow](https://www.servicenow.com/support/contact-support.html) para obter esses valores.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_general_400.png)

1. Um serviço de configuração de um clique é fornecido para o ServiceNow, ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configuração do ServiceNow**, clique em **Configurar ServiceNow** para abrir a janela de logon Configurar.

    ![Configurar o logon único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

1. Insira o nome da instância ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar o logon** e clique em **Configurar agora**. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, clique em **Configurar manualmente o logon único** e copie a **URL de logout, ID de Entidade de SAML e a URL de Serviço de Logon Único do SAML** da seção de Referência rápida.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do Aplicativo")

1. Entre no seu aplicativo ServiceNow Express como administrador.

1. No painel de navegação à esquerda, clique em **Logon Único**.

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do Aplicativo")

1. No diálogo **Logon Único**, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar URL do Aplicativo](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do Aplicativo")

    a. Ative/desative **Habilitar SSO de vários provedores** à direita.
    
    b. Ative/desative **Habilitar registro em log de depuração para a integração de SSO de vários provedores** à direita.
    
    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.

1. No diálogo **Logon Único**, clique em **Adicionar Novo Certificado**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694973ex.png "Configurar logon único")

1. No diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694975.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ativo**.

    c. Para **Formato**, selecione **PEM**.

    d. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    e. Abra seu certificado codificado em base64 baixado do portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado PEM**.

    f. Clique em **Atualizar**

1. No diálogo **Logon Único**, clique em **Adicionar novo IdP**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694976ex.png "Configurar logon único")

1. No diálogo **Adicionar Novo Provedor de Identidade**, em **Configurar Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694982ex.png "Configurar logon único")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **SAML2.0**).

    b. No campo **URL do Provedor de Identidade**, cole o valor da **ID do Provedor de Identidade** que você copiou no portal do Azure.
    
    c. No campo **AuthnRequest do Provedor de Identidade**, cole o valor da **URL da Solicitação de Autenticação** que você copiou no portal do Azure.

    d. No campo **SingleLogoutRequest do Provedor de Identidade**, cole o valor da **URL de Serviço de Logout Único** que você copiou no portal do Azure

    e. Como **Certificado de Provedor de Identidade**, selecione o certificado que você criou na etapa anterior.

1. Clique em **Configurações Avançadas** e, em **Propriedades Adicionais do Provedor de Identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694983ex.png "Configurar logon único")

    a. Na caixa de texto **associação de protocolo SingleLogoutRequest do IDP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Na caixa de texto **Política da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. No **Método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Desmarque **Criar um AuthnContextClass**.

1. Em **Propriedades Adicionais do Provedor de Serviço**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694984ex.png "Configurar logon único")

    a. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua página inicial de instância do ServiceNow.

    > [!NOTE]
    > A home page da instância do ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Na caixa de texto **ID da entidade/emissor** , digite a URL do seu locatário do ServiceNow.

    c. Na caixa de texto **URI do Público-alvo** , digite a URL do seu locatário do ServiceNow.

    d. Na caixa de texto **Distorção do Relógio**, digite **60**.

    e. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

    f. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/servicenow-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/servicenow-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/servicenow-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/servicenow-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-servicenow-test-user"></a>Criar um usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ServiceNow. O ServiceNow dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar [aqui](servicenow-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

> [!NOTE]
> Se for necessário criar um usuário manualmente, você precisará entrar em contato com a [Equipe de suporte do cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ServiceNow.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ServiceNow, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de Aplicativos](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
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
