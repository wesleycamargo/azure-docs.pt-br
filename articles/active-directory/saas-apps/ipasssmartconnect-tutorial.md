---
title: 'Tutorial: Integração do Azure Active Directory com o iPass SmartConnect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a68a208496904fcc8bfe13a227c61bf313214f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198160"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Tutorial: Integração do Azure Active Directory com o iPass SmartConnect

Neste tutorial, você aprenderá a integrar o iPass SmartConnect ao Azure AD (Azure Active Directory).

A integração do iPass SmartConnect com o Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao iPass SmartConnect.
- Você pode permitir que usuários façam logon automaticamente no iPass SmartConnect (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o iPass SmartConnect, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada por logon único do iPass SmartConnect

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando iPass SmartConnect da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Adicionando iPass SmartConnect da galeria
Para configurar a integração do iPass SmartConnect com o Azure AD, você precisa adicionar o iPass SmartConnect da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iPass SmartConnect da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **iPass SmartConnect**, selecione **iPass SmartConnect** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![iPass SmartConnect na lista de resultados](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o iPass SmartConnect, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do iPass SmartConnect é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no iPass SmartConnect.

Para configurar e testar o logon único do Azure AD com o iPass SmartConnect, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criação de um usuário de teste do iPass SmartConnect](#create-an-ipass-smartconnect-test-user)**: para ter um equivalente de Brenda Fernandes no iPass SmartConnect que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo iPass SmartConnect.

**Para configurar o logon único do Azure AD com o iPass SmartConnect, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **iPass SmartConnect**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Na seção **Domínio e URLs do iPass SmartConnect**, se você quiser configurar o aplicativo no **Modo iniciado por IDP**, não é necessário executar qualquer etapa.

    ![Informações de logon único de URLs e Domínio do iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Marque Mostrar configurações avançadas de URL e siga a etapa a seguir se desejar configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de URLs e Domínio do iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Na caixa de texto URL de Logon, digite uma URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. O aplicativo iPass SmartConnect espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o logon único](./media/ipasssmartconnect-tutorial/attribute.png)

1. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** para expandir os atributos. Realize as seguintes etapas em cada um dos atributos exibidos:

    | Nome do atributo | Valor do atributo | Valor de namespace|
    | ---------------| --------------- |----------------|
    | firstName | user.givenname |   |
    | lastName | user.surname | |
    | email | user.userprincipalname | |
    | Nome de Usuário | user.userprincipalname | |

     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Mantenha o valor do namespace em branco para essa linha.

    e. Clique em **OK**.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **iPass SmartConnect**, você precisa enviar o **XML de Metadados** baixado e o **Nome de domínio** para a [Equipe de suporte do iPass SmartConnect](mailto:help@ipass.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Criar um usuário de teste do SmartConnect iPass

Nesta seção, você deve criar um usuário chamado Brenda Fernandes no iPass SmartConnect. Trabalhe com a  [equipe de suporte do iPass SmartConnect](mailto:help@ipass.com)  para adicionar os usuários ou o domínio que precisa estar na lista de permissões na plataforma do iPass SmartConnect. Se o domínio for adicionado pela equipe, os usuários serão automaticamente provisionados à plataforma iPass SmartConnect. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao iPass SmartConnect.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao iPass SmartConnect, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **iPass SmartConnect**.

    ![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

**Para testar o aplicativo no fluxo iniciado pelo SP, execute as seguintes etapas:**

 a. Baixe o cliente windows iPass SmartConnect [aqui](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instale o cliente e execute a inicialização.

c. Clique em **Introdução**.

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Insira o nome de usuário do Azure com o domínio. Clique em **Continuar**. Isso será redirecionado à página de logon do Azure

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Após a autenticação bem-sucedida, a ativação do cliente será iniciada. O cliente será ativado.

**Para testar o aplicativo no fluxo iniciado pelo IdP, execute as seguintes etapas:**

 a. Faça logon em [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Clique no aplicativo iPass SmartConnect.

c. Isso inicia a página SSA. Clique em **Baixar o Aplicativo para Windows** para instalar o cliente iPass SmartConnect.

![O link do iPass SmartConnect na lista de Aplicativos](./media/ipasssmartconnect-tutorial/testing4.png)

d. Após a instalação, o cliente na primeira inicialização iniciará automaticamente a ativação após aceitar os termos e condições.

e. Se a ativação não for iniciada, clique no botão Ativar na página SSA para iniciar a ativação.

f. O cliente será ativado.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

