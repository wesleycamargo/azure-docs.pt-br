---
title: 'Tutorial: Integração do Azure Active Directory com o SAP NetWeaver | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156741"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Tutorial: Integração do Azure Active Directory com o SAP NetWeaver

Neste tutorial, você aprenderá a integrar o SAP NetWeaver ao Azure AD (Azure Active Directory).

A integração do SAP NetWeaver ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory que tem acesso ao SAP NetWeaver.
- Você pode permitir que seus usuários façam logon automaticamente no SAP NetWeaver (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP NetWeaver, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para logon único do SAP NetWeaver
- SAP NetWeaver V7.20 necessário pelo menos

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
 O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar SAP NetWeaver da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionar SAP NetWeaver da galeria

Para configurar a integração do SAP NetWeaver ao Azure AD, você precisará adicionar o SAP NetWeaver da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP NetWeaver da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP NetWeaver**, selecione **SAP NetWeaver** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SAP NetWeaver na lista de resultados](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP NetWeaver, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP NetWeaver é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP NetWeaver.

Para configurar e testar o logon único do Azure AD com o SAP NetWeaver, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do SAP NetWeaver](#creating-sapnetweaver-test-user)** – para ter um equivalente de Brenda Fernandes no SAP NetWeaver que esteja vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do SAP NetWeaver.

**Para configurar o logon único do Azure AD com o SAP NetWeaver, execute as seguintes etapas:**

1. Abra uma nova janela do navegador da Web e faça login no site da empresa SAP NetWeaver como administrador

2. Certifique-se de que os serviços **http** e **https** estão ativos e as portas apropriadas são atribuídas no código T **SMICM**.

3. Faça logon no cliente de negócios do Sistema SAP (T01), em que o SSO é necessário e ative o Gerenciamento de sessão de segurança HTTP.

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

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ative os serviços SICF abaixo:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Vá para o código de transação **SAML2** no cliente de negócios do sistema SAP [T01/122]. Isso abrirá uma interface do usuário em um navegador. Neste exemplo, supomos 122 como cliente do SAP Business.

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Forneça seu nome de usuário e senha para entrar na interface do usuário e clique em **Editar**.

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Substitua **Nome do Provedor** de T01122 para `http://T01122` e clique em **Salvar**.

    > [!NOTE]
    > Como nome do provedor vem como <sid><client> formato mas o Azure AD espera nome no formato <protocol>://<name>, recomendadas manter o nome do provedor como https://<sid> <client> para permitir que vários mecanismos SAP NetWeaver configurem no Azure Active Directory.

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Geração de Metadados do Provedor de Serviço**: - uma vez que terminamos de configurar as configurações do **Provedor Local** e **Provedores Confiáveis** na Interface de usuário do SAML 2.0, a próxima etapa seria gerar arquivo de metadados do provedor de serviços (que contém todas as configurações, contextos de autenticação e outras configurações no SAP). Depois que esse arquivo é gerado, precisamos carregá-lo no Microsoft Azure Active Directory.

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

     a. Vá para a **guia do Provedor Local**.

    b. Clique em **Metadados**.

    c. Salvar o**arquivo XML de Metadados** em seu computador e carregá-lo na seção **Configuração Básica do SAML** automaticamente preencher o **Identificador** e os valores de **Responder URL** no portal do Azure.

8. No portal do Azure, na página de integração do aplicativo **SAP NetWeaver**, clique em **Logon único**.

    ![Link Configurar logon único][4]

9. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

10. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

11. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

     a. Clique em **Carregar arquivo de metadados** para carregar o **Arquivo de metadados do provedor de serviços** que você obteve anteriormente.

    ![Carregar arquivo de metadados](common/editmetadataupload.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Carregar arquivo de metadados](common/uploadmetadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores **Identificador** e **URL de resposta** serão populados automaticamente na caixa de texto da seção **Configuração Básica do SAML**, conforme mostrado abaixo:

    ![Informações de logon único de Domínio do SAP NetWeaver e URLs](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<your company instance of SAP NetWeaver>`

12. O aplicativo SAP NetWeaver espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Seção Atributo](./media/sapnetweaver-tutorial/edit_attribute.png)

13. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

     a. Clique no ícone **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.
    
    ![Seção Atributo](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Na guia **Gerenciar declarações do usuário**, realize as seguintes etapas:

    ![Seção Atributo](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Selecione **Transformação**.
  
    * Na lista **Transformação**, selecione `ExtractMailPrefix()`.
  
    * Na lista **Parâmetro 1**, selecione `user.userprincipalname`.

    * Clique em **Salvar**.

14. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Na seção**Configurar SAP NetWeaver**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do SAP NetWeaver](common/configuresection.png)

16. Faça logon no sistema SAP e vá para o código de transação SAML2. Isso abre a nova janela do navegador com a tela de configuração do SAML.

17. Para configurar pontos de extremidade para provedor de Identidade confiável (Microsoft Azure Active Directory) para a guia **Provedores Confiáveis**.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Pressione **Add** e selecione **Carregar arquivo de metadados** no menu de contexto.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Carregue o arquivo de metadados que você fez o download do portal do Azure.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Na próxima tela, digite o nome do alias. Por exemplo, aadsts e pressione **Próxima** para continuar.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Certifique-se de que seu **Algoritmo de código hash** deve ser **SHA-256** e não requer alterações e pressione **Próxima**.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. Nos **pontos de extremidade de logon único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. Nos **pontos de extremidade de logout único**, use **HTTP POST** e clique em **Próxima** para continuar.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. Nos **pontos de extremidade de artefato**, aperte **Próxima** para continuar.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. Em **Requisitos de Autenticação**, clique em **Concluir**.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Vá para a guia **Provedor Confiável** > **Federação de Identidades** (na parte inferior da tela). Clique em **Editar**.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Clique em **Adicionar** na guia **Federação de Identidades** (janela inferior).

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. Na janela pop-up selecione **Não especificado** em **Formatos com suporte NameID** e clique em OK.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Observe que os valores de **Fonte de ID de usuário** e **modo de mapeamento de id de usuário** determinam o vínculo entre um usuário do SAP e a declaração do Azure Active Directory.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: o usuário do SAP para o mapeamento de usuário do Azure AD.

     a. Captura de tela de detalhes de NameID do SAP.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Captura de tela mencionando as declarações necessárias do Microsoft Azure Active Directory.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: selecione a ID de usuário do SAP com base no endereço de email configurado no SU01. Nesse caso, a ID de email deve ser configurado no su01 para cada usuário que requer o SSO.

     a.  Captura de tela de detalhes de NameID do SAP.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Captura de tela mencionando as declarações necessárias do Microsoft Azure Active Directory.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Clique em **Salvar** e, em seguida, clique em **Habilitar** para habilitar o provedor de identidade.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/configuration1.png)

31. Clique em **OK** quando solicitado.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-sap-netweaver-test-user"></a>Criação de um usuário de teste do SAP NetWeaver

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP NetWeaver. Trabalhe sua equipe de especialistas SAP internamente ou trabalhe com seu parceiro do SAP de organização para adicionar os usuários à plataforma SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP NetWeaver.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SAP NetWeaver**.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

1. Depois que o provedor de identidade do Microsoft Azure Active Directory tiver sido ativado, tente acessar abaixo da URL para verificar o SSO (não haverá nenhum prompt de nome de usuário e senha)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) use a URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua sapurl pelo nome de host real do SAP.

2. A URL acima deve levar você para a tela mencionada abaixo. Se você for capaz de alcançar a página abaixo, a instalação do SSO do Microsoft Azure Active Directory é feita com êxito.

    ![Configurar o logon único](./media/sapnetweaver-tutorial/testingsso.png)

3. Se ocorrer o prompt de nome de usuário e senha, diagnostique o problema ap habilitar o rastreamento usando a URL abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
