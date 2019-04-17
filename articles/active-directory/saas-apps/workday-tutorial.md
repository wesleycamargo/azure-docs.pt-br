---
title: 'Tutorial: Integração do Azure Active Directory com o Workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278523"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração do Active Directory do Azure com o Workday

Neste tutorial, você aprenderá como integrar o Workday ao Azure AD (Azure Active Directory).
A integração do Workday com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Workday.
* Você pode permitir que seus usuários entrem automaticamente no Workday (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Workday, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Workday com logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Workday dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-workday-from-the-gallery"></a>Adicionar o Workday da galeria

Para configurar a integração do Workday com o Azure AD, é necessário adicionar o Workday da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workday da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Workday**, selecione **Workday** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Workday na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Workday com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workday.

Para configurar e testar o logon único do Azure AD com o Workday, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Workday](#configure-workday-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Workday](#create-workday-test-user)** – para ter um equivalente de Brenda Fernandes no Workday que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Workday, siga as etapas abaixo:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Workday**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Workday](common/sp-identifier.png)

     a. Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão: `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.workday.com`

5. Clique em **Definir URLs adicionais** e execute a seguinte etapa:

    ![Informações de logon único de Domínio e URLs do Workday](./media/workday-tutorial/reply.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https:\//impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Esses não são os valores reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Sua URL de resposta deve ter um subdomínio, por exemplo: www, wd2, wd3, wd3-impl, wd5, wd5-impl.
    > Usar algo como `http://www.myworkday.com` funciona, mas `http://myworkday.com` não. Contate a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Seu aplicativo Workday espera as instruções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Workday espera que **nameidentifier** seja mapeado com **user.mail**, **UPN** etc. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Aqui nós Mapeamos a identificação do nome UPN (userPrincipalName) como padrão. Você precisa para mapear a ID de nome com a ID de usuário real em sua conta do Workday (seu email, etc. o UPN) para trabalhar com êxito do SSO.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Workday**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-workday-single-sign-on"></a>Configurar o logon único do Workday

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Workday como administrador.

2. Na **Caixa de pesquisa**, procure com o nome **Editar Configuração de Locatário – Segurança** no lado superior esquerdo da home page.

    ![Editar segurança de locatário](./media/workday-tutorial/IC782925.png "Editar segurança de locatário")

3. Na seção **URLs de Redirecionamento** , execute as seguintes etapas:

    ![URLs de redirecionamento](./media/workday-tutorial/IC7829581.png "URLs de redirecionamento")

     a. Clique em **Adicionar Linha**.

    b. Nas caixas de texto **URL de Redirecionamento de Logon** e **URL de Redirecionamento Móvel**, digite a **URL de Entrada** inserida na página **Configuração Básica de SAML** do Portal do Azure.

    c. No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logoff** e, em seguida, cole-a na caixa de texto **URL de Redirecionamento de Logoff**.

    d. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.  

   > [!NOTE]
   > O valor do atributo Ambiente é vinculado ao valor da URL do locatário:  
   > –Se o nome de domínio da URL do locatário do Workday começar com impl, por exemplo, *https:\//impl.workday.com/\<tenant\>/login-saml2.flex*, o atributo **Ambiente** deverá ser definido como Implementação.  
   > –Se o nome de domínio começar de outra forma, será necessário contatar a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor de **Ambiente** correspondente.

4. Na seção **Configuração do SAML** , execute as seguintes etapas:

    ![Instalação do SAML](./media/workday-tutorial/IC782926.png "Instalação do SAML")

     a.  Selecione **Habilitar Autenticação SAML**.

    b.  Clique em **Adicionar Linha**.

5. Na seção **Provedores de Identidade do SAML**, execute as seguintes etapas:

    ![Provedores de Identidade SAML](./media/workday-tutorial/IC7829271.png "Provedores de Identidade SAML")

     a. Na caixa de texto **Nome do Provedor de Identidade**, digite um nome de provedor (por exemplo: *SPInitiatedSSO*).

    b. No Portal do Azure, na janela **Configurar Workday**, o valor do **Identificador do Azure AD** e, em seguida, cole-o na caixa de texto **Emissor**.

    ![Provedores de Identidade SAML](./media/workday-tutorial/IC7829272.png "Provedores de Identidade SAML")

    c. No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logoff** e, em seguida, cole-a na caixa de texto **URL de Resposta de Logoff**.

    d. No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logon** e, em seguida, cole-a na caixa de texto **URL do Serviço de SSO do IdP**.

    e. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.

    f. Clique em **Certificado de Chave Pública do Provedor de Identidade** e em **Criar**.

    ![Criar](./media/workday-tutorial/IC782928.png "Criar")

    g. Clique em **Criar Chave Pública x509**.

    ![Criar](./media/workday-tutorial/IC782929.png "Criar")

6. Na seção **Exibir Chave Pública x509** , realize as seguintes etapas:

    ![Exibir chave pública x509](./media/workday-tutorial/IC782930.png "Exibir chave pública x509")

     a. Na caixa de texto **Nome**, digite um nome para o seu certificado (por exemplo: *PPE\_SP*).

    b. Na caixa de texto **Válido de** , digite o valor do atributo “válido de” do seu certificado.

    c.  Na caixa de texto **Válido até** , digite o valor do atributo “válido até” do seu certificado.

    > [!NOTE]
    > Você pode obter as datas de “válido a partir de” e “válido até” do certificado baixado clicando duas vezes nele.  As datas são listadas na guia **Detalhes** .
    >
    >

    d.  Abra seu certificado codificado em base-64 no bloco de notas e copie o conteúdo dele.

    e.  Na caixa de texto **Certificado** , cole o conteúdo da área de transferência.

    f.  Clique em **OK**.

7. Execute as seguintes etapas:

    ![Configuração de SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuração de SSO")

     a.  Na caixa de texto **ID do Provedor de Serviço**, digite **https://www.workday.com**.

    b. Selecione **Não Esvazie a Solicitação de Autenticação iniciada por SP**.

    c. Para **Solicitação de Método de Autenticação de Assinatura** , selecione **SHA256**.

    ![Método de assinatura da solicitação de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura da solicitação de autenticação") 

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Verifique se que você configurar o logon único corretamente. Caso você habilite o logon único com a configuração incorreta, talvez não seja possível inserir o aplicativo com suas credenciais e ser bloqueado. Nessa situação, o Workday fornece uma URL de login de backup em que os usuários podem entrar usando seu nome de usuário e senha normais no seguinte formato: [Your Workday URL] /login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Workday.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Workday**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workday**.

    ![O link do Workday na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-workday-test-user"></a>Criar um usuário de teste do Workday

Nesta seção, você cria um usuário chamado Brenda Fernandes no Workday. Trabalhe com [a equipe de suporte do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os usuários à plataforma Workday. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Workday no Painel de Acesso, você deverá ser conectado automaticamente ao Workday, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

