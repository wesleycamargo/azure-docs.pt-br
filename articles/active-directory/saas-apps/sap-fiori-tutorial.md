---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Fiori | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: e94fe3156677a507eab91eee339ed29bf7b4ad2e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257630"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Tutorial: Integração do Azure Active Directory ao SAP Fiori

Neste tutorial, você aprenderá a integrar o SAP Fiori ao Azure AD (Azure Active Directory).
A integração do SAP Fiori ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD que tem acesso ao SAP Fiori.
* Você pode permitir que os usuários sejam conectados automaticamente ao SAP Fiori (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Fiori, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SAP Fiori
* SAP Fiori V7.20 necessário (mínimo)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Fiori dá suporte ao SSO iniciado por **SP**

## <a name="adding-sap-fiori-from-the-gallery"></a>Como adicionar o SAP Fiori por meio da galeria

Para configurar a integração do SAP Fiori ao Azure AD, você precisará adicionar o SAP Fiori por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Fiori por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP Fiori**, selecione **SAP Fiori** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SAP Fiori na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Fiori, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Fiori.

Para configurar e testar o logon único do Azure AD com o SAP Fiori, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SAP Fiori](#configure-sap-fiori-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SAP Fiori](#create-sap-fiori-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Fiori que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP Fiori, execute as seguintes etapas:

1. Abra uma nova janela do navegador da Web e entre em seu site de empresa do SAP Fiori como administrador

2. Certifique-se de que os serviços **http** e **https** estão ativos e as portas apropriadas são atribuídas no código T **SMICM**.

3. Entre no cliente de negócios do Sistema SAP (T01), em que o SSO é necessário e ative o Gerenciamento de sessão de Segurança HTTP.

     a. Vá para o código de transação **SICF_SESSIONS**. Ele exibe todos os parâmetros de perfil relevante com os valores atuais. Serão apresentados como abaixo:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Ajuste acima dos parâmetros de acordo com os requisitos da sua organização, acima dos parâmetros são fornecidos aqui como indicação apenas.

    b. Se for necessário, ajuste os parâmetros no perfil/padrão da instância do sistema SAP e reinicie o sistema SAP.

    c. Clique duas vezes no cliente relevantes para habilitar a sessão de segurança HTTP.

    ![O link de download do Certificado](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    d. Ative os serviços SICF abaixo:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Vá para o código de transação **SAML2** no cliente de negócios do sistema SAP [T01/122]. Isso abrirá uma interface do usuário em um navegador. Neste exemplo, supomos 122 como cliente do SAP Business.

    ![O link de download do Certificado](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

5. Forneça seu nome de usuário e senha para entrar na interface do usuário e clique em **Editar**.

    ![O link de download do Certificado](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

6. Substitua **Nome do Provedor** de T01122 para `http://T01122` e clique em **Salvar**.

    > [!NOTE]
    > Por padrão, o nome do provedor vem no formato <sid><client>, mas o Azure AD espera o nome no formato <protocol>://<name>, recomendando manter o nome do provedor como https://<sid><client> para permitir que vários mecanismos do ABAP do SAP Fiori realizem configurações no Azure AD.

    ![O link de download do Certificado](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

7. **Geração de Metadados do Provedor de Serviço**: - uma vez que terminamos de configurar as configurações do **Provedor Local** e **Provedores Confiáveis** na Interface de usuário do SAML 2.0, a próxima etapa seria gerar arquivo de metadados do provedor de serviços (que contém todas as configurações, contextos de autenticação e outras configurações no SAP). Depois que esse arquivo é gerado, precisamos carregá-lo no Microsoft Azure Active Directory.

    ![O link de download do Certificado](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

     a. Vá para a **guia do Provedor Local**.

    b. Clique em **Metadados**.

    c. Salve o **arquivo XML de Metadados** gerado no computador e carregue-o na seção **Configuração Básica do SAML** para popular automaticamente os valores de **Identificador** e **URL de Resposta** no portal do Azure.

8. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Fiori**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

9. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

10. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

11. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

     a. Clique em **Carregar arquivo de metadados** para carregar o **Arquivo de metadados do provedor de serviços** que você obteve anteriormente.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na caixa de texto da seção **Configuração Básica do SAML**, conforme mostrado abaixo:

    ![Informações de logon único de Domínio e URLs do SAP Fiori](common/sp-identifier-reply.png)

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<your company instance of SAP Fiori>`

    > [!NOTE]
    > Já observamos que alguns clientes relataram um erro de URL de Resposta incorreta configurada para suas instâncias. Se você receber um erro desse tipo, use o seguinte script do PowerShell como uma solução alternativa para definir a URL de Resposta correta para a instância:
    ```
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    ``` 
    > A ID de Objeto da ServicePrincipal deve ser definida primeiro por você ou também pode ser passada aqui.

12. O aplicativo SAP Fiori espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

13. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

     a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![image](./media/sapfiori-tutorial/nameidattribute.png)

    ![image](./media/sapfiori-tutorial/nameidattribute1.png)

    b. Na lista **Transformação**, selecione **ExtractMailPrefix()**.

    c. Na lista **Parâmetro 1**, selecione **user.userprinicipalname**.

    d. Clique em **Save** (Salvar).

14. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

15. Na seção **Configurar o SAP Fiori**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sap-fiori-single-sign-on"></a>Configurar o logon único do SAP Fiori

1. Entre no sistema SAP e acesse o código de transação SAML2. Isso abre a nova janela do navegador com a tela de configuração do SAML.

2. Para configurar pontos de extremidade para provedor de Identidade confiável (Microsoft Azure Active Directory) para a guia **Provedores Confiáveis**.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

3. Pressione **Add** e selecione **Carregar arquivo de metadados** no menu de contexto.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

4. Carregue o arquivo de metadados que você fez o download do portal do Azure.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

5. Na próxima tela, digite o nome do alias. Por exemplo, aadsts e pressione **Próxima** para continuar.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

6. Certifique-se de que seu **Algoritmo de código hash** deve ser **SHA-256** e não requer alterações e pressione **Próxima**.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

7. Nos **pontos de extremidade de logon único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

8. Nos **pontos de extremidade de logout único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

9. Nos **pontos de extremidade de artefato**, aperte **Próxima** para continuar.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

10. Em **Requisitos de Autenticação**, clique em **Concluir**.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

11. Vá para a guia **Provedor Confiável** > **Federação de Identidades** (na parte inferior da tela). Clique em **Editar**.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

12. Clique em **Adicionar** na guia **Federação de Identidades** (janela inferior).

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

13. Na janela pop-up selecione **Não especificado** em **Formatos com suporte NameID** e clique em OK.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

14. Observe que os valores de **Fonte de ID de usuário** e **modo de mapeamento de ID de usuário** determinam o vínculo entre o usuário do SAP e a declaração do Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: o usuário do SAP para o mapeamento de usuário do Azure AD.

     a. Captura de tela de detalhes da NameID do SAP.

    ![Configurar o logon único](./media/sapfiori-tutorial/nameiddetails.png)

    b. Captura de tela mencionando as declarações Obrigatórias do Azure AD.

    ![Configurar o logon único](./media/sapfiori-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione a ID de usuário do SAP com base no endereço de email configurado no SU01. Nesse caso, a ID de email deve ser configurada no su01 para cada usuário que precisar do SSO.

     a.  Captura de tela de detalhes da NameID do SAP.

    ![Configurar o logon único](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    b. captura de tela mencionando as declarações Obrigatórias do Azure AD.

    ![Configurar o logon único](./media/sapfiori-tutorial/claimsaad2.png)

15. Clique em **Salvar** e, em seguida, clique em **Habilitar** para habilitar o provedor de identidade.

    ![Configurar o logon único](./media/sapfiori-tutorial/configuration1.png)

16. Clique em **OK** quando solicitado.

    ![Configurar o logon único](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SAP Fiori.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SAP Fiori**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Fiori**.

    ![O link do SAP Fiori na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-fiori-test-user"></a>Criar um usuário de teste do SAP Fiori

Nesta seção, você criará um usuário chamado Brenda Fernandes no SAP Fiori. Trabalhe com sua equipe interna de especialistas SAP ou com o parceiro SAP de sua organização para adicionar os usuários à plataforma SAP Fiori.

### <a name="test-single-sign-on"></a>Testar logon único

1. Depois que o provedor de identidade do Microsoft Azure Active Directory tiver sido ativado, tente acessar abaixo da URL para verificar o SSO (não haverá nenhum prompt de nome de usuário e senha)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) use a URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua sapurl pelo nome de host real do SAP.

2. A URL acima deve levar você para a tela mencionada abaixo. Se você for capaz de alcançar a página abaixo, a instalação do SSO do Microsoft Azure Active Directory é feita com êxito.

    ![Configurar o logon único](./media/sapfiori-tutorial/testingsso.png)

3. Se ocorrer o prompt de nome de usuário e senha, diagnostique o problema ap habilitar o rastreamento usando a URL abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)