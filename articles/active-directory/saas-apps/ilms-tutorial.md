---
title: 'Tutorial: Integração do Azure Active Directory ao iLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d528195161b987dda783c4b22721e950f702f94
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984451"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutorial: Integração do Azure Active Directory ao iLMS

Neste tutorial, você aprende a integrar o iLMS ao Azure AD (Azure Active Directory).
A integração do iLMS ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao iLMS.
* Você pode permitir que os usuários sejam conectados automaticamente ao iLMS (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iLMS, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do iLMS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O iLMS dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-ilms-from-the-gallery"></a>Adicionando o iLMS por meio da galeria

Para configurar a integração do iLMS ao Azure AD, você precisa adicionar o iLMS à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o iLMS por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **iLMS**, selecione **iLMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![iLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o iLMS, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iLMS.

Para configurar e testar o logon único do Azure AD com o iLMS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do iLMS](#configure-ilms-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do iLMS](#create-ilms-test-user)** – para ter um equivalente de Brenda Fernandes no iLMS que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o iLMS, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iLMS**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do iLMS](common/idp-intiated.png)

     a. Na caixa de texto **Identificador**, cole o valor do **Identificador** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS.

    b. Na caixa de texto **URL de Resposta**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS com o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do iLMS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Para habilitar o provisionamento JIT, o aplicativo iLMS espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Você precisa habilitar a opção **Criar Conta de Usuário Não Reconhecido** no iLMS para mapear esses atributos. Siga as instruções [aqui](http://support.inspiredelearning.com/customer/portal/articles/2204526) para ter uma ideia sobre a configuração de atributos.

7. Além do indicado acima, o aplicativo iLMS espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o iLMS**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-ilms-single-sign-on"></a>Configurar o logon único do iLMS

1. Em outra janela do navegador da Web, faça logon no **portal de administração do iLMS** como administrador.

2. Clique em **SSO:SAML** na guia **Configurações** para abrir as configurações do SAML e realize as seguintes etapas:

    ![Configurar o logon único](./media/ilms-tutorial/1.png)

3. Expanda a seção **Provedor de Serviços** e copie o valor do **Identificador** e da **(URL) do Ponto de Extremidade**.

    ![Configurar o logon único](./media/ilms-tutorial/2.png) 

4. Na seção **Provedor de Identidade**, clique em **Importar Metadados**.

5. Selecione o arquivo de **Metadados de Federação** baixado no portal do Azure na seção **Certificado de Autenticação SAML**.

    ![Configurar o logon único](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se você desejar habilitar o provisionamento JIT para criar contas do iLMS para cancelar o reconhecimento de usuários, realize as seguintes etapas:

     a. Marque a opção **Criar Conta de Usuário Não Reconhecido**.

    ![Configurar o logon único](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapeie os atributos no Azure AD com os atributos no iLMS. Na coluna de atributos, especifique o nome dos atributos ou o valor padrão.

    c. Acesse a guia **Regras de Negócios** e realize as seguintes etapas:

    ![Configurar o logon único](./media/ilms-tutorial/5.png)

    d. Marque a opção **Criar Regiões, Divisões e Departamentos Não Reconhecidos** para criar Regiões, Divisões e Departamentos que ainda não existem no momento do Logon Único.

    e. Marque a opção **Atualizar Perfil de Usuário Durante a Conexão** para especificar se o perfil do usuário é atualizado a cada Logon Único.

    f. Se a opção **Atualizar Valores em Branco para Campos Não Obrigatórios no Perfil do Usuário** estiver marcada, os campos de perfil opcionais que estiverem em branco após a conexão também farão com que o perfil do iLMS do usuário contenha valores em branco nesses campos.

    g. Marque a opção **Enviar Email de Notificação de Erro** e insira o email do usuário em que você deseja receber o email de notificação de erro.

7. Clique no botão **Salvar** para salvar as alterações.

    ![Configurar o logon único](./media/ilms-tutorial/save.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao iLMS.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **iLMS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **iLMS**.

    ![O link do iLMS na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ilms-test-user"></a>Criar um usuário de teste do iLMS

O aplicativo dá suporte ao provisionamento de usuário Just-In-Time e, após a autenticação, os usuários são criados no aplicativo automaticamente. O JIT funcionará se você tiver clicado na caixa de seleção **Criar Conta de Usuário Não Reconhecido** durante a definição da configuração do SAML no portal de administração do iLMS.

Caso precise criar um usuário manualmente, siga as etapas abaixo:

1. Faça logon no site da empresa do iLMS como administrador.

2. Clique em **Registrar Usuário** na guia **Usuários** para abrir a página **Registrar Usuário**.

   ![Adicionar Funcionário](./media/ilms-tutorial/3.png)

3. Na página **Registrar Usuário**, execute as etapas a seguir.

    ![Adicionar Funcionário](./media/ilms-tutorial/create_testuser_add.png)

     a. Na caixa de texto **Nome**, digite o nome do usuário como Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como Fernandes.

    c. Na caixa de texto **ID de Email**, digite o endereço de email do usuário como BrittaSimon@contoso.com.

    d. Na lista suspensa **Região**, selecione o valor da região.

    e. Na lista suspensa **Divisão**, selecione o valor da divisão.

    f. Na lista suspensa **Departamento**, selecione o valor do departamento.

    g. Clique em **Salvar**.

    > [!NOTE]
    > Você pode enviar o email de registro para o usuário selecionando a caixa de seleção **Enviar Email de Registro**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do iLMS no Painel de Acesso, você deverá ser conectado automaticamente ao iLMS, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)