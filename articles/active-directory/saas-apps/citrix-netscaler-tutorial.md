---
title: 'Tutorial: Integração do Azure Active Directory com Citrix Netscaler | Microsoft Docs'
description: Saiba como configurar logon único entre Azure Active Directory e Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281668"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Tutorial: Integração do Azure Active Directory com Citrix Netscaler

Neste tutorial, você aprenderá como integrar o Citrix Netscaler com Azure AD (Azure Active Directory).
A integração do Citrix Netscaler com Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Citrix Netscaler.
* Você pode permitir que os usuários sejam conectados automaticamente ao Citrix Netscaler (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Citrix Netscaler, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Citrix Netscaler

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Citrix Netscaler dá suporte ao SSO iniciado por **SP**

* O Citrix Netscaler é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Adicionar o Citrix Netscaler da galeria

Para configurar a integração do Citrix Netscaler com Azure AD, será necessário adicionar o Citrix Netscaler da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Citrix Netscaler da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Citrix Netscaler**, selecione **Citrix Netscaler** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Citrix Netscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Citrix Netscaler, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix Netscaler.

Para configurar e testar o logon único do Azure AD com o Citrix Netscaler, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Citrix Netscaler](#configure-citrix-netscaler-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Citrix Netscaler](#create-citrix-netscaler-test-user)** – para ter um equivalente de Brenda Fernandes no Citrix Netscaler que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar logon único do Azure AD com Citrix Netscaler, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Citrix Netscaler**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Citrix Netscaler](common/sp-identifier-reply.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>`

    c. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

    > [!NOTE]
    > Para que o SSO funcione, essas URLs deverão ser acessíveis a partir de sites públicos. É necessário habilitar o firewall ou outras configurações de segurança do Netscaler para permitir que o Azure AD publique o token na URL de ACS configurado.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Citrix Netscaler** copie as URLs apropriadas, conforme sua necessidade.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-citrix-netscaler-single-sign-on"></a>Configurar logon único do Citrix Netscaler

1. Em uma janela diferente do navegador da Web, conecte o locatário do Citrix Netscaler como um administrador.

2. Certifique-se de que a **Versão do Firmware do NetScaler = NS12.1: Build 48.13.nc**.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure01.png)

3. Na página **Servidor Virtual de VPN**, execute as seguintes etapas:

     ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure02.png)

     a. Defina as configurações do Gateway **Somente ICA** como **verdadeiro**.
    
    b. Defina **Habilitar Autenticação** como **verdadeiro**.
    
    c. **DTLS** é opcional.
    
    d. Certifique-se de que **SSLv3** esteja **Desabilitado**.

4. Um Grupo **Codificações de SSL** personalizado é criado para atingir A+ em https://www.ssllabs.com, conforme mostrado abaixo:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure03.png)

5. Na página **Configurar Servidor SAML de Autenticação**, execute as seguintes etapas:

      ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure04.png)

     a. Na caixa de texto **Nome**, digite o nome do servidor.

    b. Na caixa de texto **Redirecionar URL**, cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. Na caixa de texto **URL de Logoff Único**, cole o valor da **URL de Logoff** que você copiou do portal do Azure.

    d. Em **Nome do Certificado IDP**, clique no sinal **"+"** para adicionar o certificado que você baixou do portal do Azure. Após o upload, selecione o certificado na lista suspensa.

    e. Os seguintes campos devem ser definidos nesta página

      ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure24.png)

    f. Selecione **Contexto de Autenticação Solicitado** como **Exato**.

    g. Selecione **Algoritmo de Assinatura** como **RSA-SHA256**.

    h. Selecione **Método Digest** como **SHA256**.

    i. Verifique **Impor nome de usuário**.

    j. Clique em **OK**

6. Para configurar o **Perfil de Sessão**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure06.png)

     a. Na caixa de texto **Nome**, digite o nome do perfil de sessão.

    b. Na guia **Experiência do Cliente**, faça as alterações conforme mostrado na captura de tela abaixo.

    c. Continue fazendo as alterações na **guia Geral**, conforme mostrado abaixo, e clique em **OK**

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure07.png)

    d. Na guia **Aplicativos Publicados**, faça as alterações conforme mostrado na captura de tela abaixo e clique em **OK**.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure08.png)

    e. Na guia **Segurança**, faça as alterações conforme mostrado na captura de tela abaixo e clique em **OK**.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure09.png)

7. Faças as Conexões de ICA na Porta de Confiabilidade de Sessão **2598**, conforme mostrado na captura de tela abaixo.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure10.png)

8. Na seção **SAML**, adicione os **Servidores**, conforme mostrado na captura de tela abaixo.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure11.png)

9. Na seção **SAML**, adicione as **Políticas**, conforme mostrado na captura de tela abaixo.

     ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure12.png)

10. Na página **Configurações Globais**, vá para a seção **Acesso sem Clientes**.

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure13.png)

11. Na guia **Configuração**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure14.png)

     a. Selecione **Permitir Domínios**.

    b. Na caixa de texto **Nome de Domínio**, selecione o domínio.

    c. Clique em **OK**.

12. Defina as Configurações de **Vitrine** no **Receptor para Sites da Web**, conforme mostrado na captura de tela abaixo:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure15.png)

13. No item pop-up **Gerenciar Métodos de Autenticação - Corp**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure16.png)

     a. Selecione **Nome de usuário e senha**.

    b. Selecione **Passagem do Gateway NetScaler**.

    c. Clique em **OK**.

14. No item pop-up **Configurar Domínios Confiáveis**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure17.png)

     a. Selecione **somente domínios confiáveis**.

    b. Clique em **Adicionar** para adicionar o domínio na caixa de texto **Domínios confiáveis**.

    c. Selecione Domínio Padrão na lista **Domínio Padrão**.

    d. Selecione **Mostrar lista de domínios na página de logon**.

    e. Clique em **OK**.

15. No item pop-up **Gerenciar Gateways NetScaler**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure18.png)

     a. Clique em **Adicionar** para adicionar os Gateways NetScaler na caixa de texto **Gateways NetScaler**.

    b. Clique em **fechar**

16. Na guia **Configurações Gerais de Vitrine**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure19.png)

     a. Na caixa de texto **Nome de exibição**, digite o nome do Gateway NetScaler.

    b. Na caixa de texto **URL do Gateway NetScaler**, digite a URL do Gateway NetScaler.

    c. Selecione **Uso ou função** como **Autenticação e roteamento HDX**.

    d. Clique em **OK**.

17. Na guia **Autoridade de Tíquete Seguro de Vitrine**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure20.png)

     a. Clique no botão **Adicionar**para adicionar as **URLs de Autoridade de Tíquete Seguro** na caixa de texto.

    b. Selecione **Habilitar confiabilidade de sessão**.

    c. Clique em **OK**.

18. Na guia **Configurações de Autenticação de Vitrine**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure21.png)

     a. Selecione a **Versão**.

    b. Selecione **Tipo de logon** como **Domínio**.

    c. Insira a **URL de Retorno de Chamada**.

    d. Clique em **OK**.

19. Na guia **Receptor Citrix de Implantação de Vitrine**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure22.png)

     a. Selecione **Opção de implantação** como **Usar receptor para HTML5, se o receptor local estiver indisponível**.

    b. Clique em **OK**.

20. No item pop-up **Gerenciar Sinalizadores**, execute as seguintes etapas:

    ![Configurar o logon único](./media/citrix-netscaler-tutorial/configure23.png)

     a. Selecione o **Sinalizador interno** como **Usar a URL de serviço**.

    b. Clique em **Adicionar** para adicionar as URLs na caixa de texto **Sinalizadores externos**.

    c. Clique em **OK**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Citrix Netscaler.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **Citrix Netscaler**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Citrix Netscaler**.

    ![O link do Citrix Netscaler na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-citrix-netscaler-test-user"></a>Criar usuário de teste do Citrix Netscaler

Nesta seção, um usuário chamado Brenda Fernandes é criado no Citrix Netscaler. O Citrix Netscaler dá suporte para provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Citrix Netscaler, um novo será criado após a autenticação.

>[!NOTE]
>Se for necessário criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Citrix Netscaler no Painel de Acesso, você deverá ser conectado automaticamente ao Citrix Netscaler para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

