---
title: 'Tutorial: Integração do Azure Active Directory ao Infogix Data3Sixty Govern | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Infogix Data3Sixty Govern.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3e54ade44828bf1e26c310a14ae401fe8ae33229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437654"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Tutorial: Integração do Azure Active Directory ao Infogix Data3Sixty Govern

Neste tutorial, você aprenderá a integrar o Infogix Data3Sixty Govern ao Azure AD (Azure Active Directory).

A integração do Infogix Data3Sixty Govern ao Microsoft Azure Active Directory proporciona os seguintes benefícios:

- No Microsoft Azure Active Directory, você pode controlar quem tem acesso ao Infogix Data3Sixty Govern.
- Você pode permitir que seus usuários entrem automaticamente no Infogix Data3Sixty Govern (Logon Único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Infogix Data3Sixty Govern, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Infogix Data3Sixty Govern

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Infogix Data3Sixty Govern a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Adicionar Infogix Data3Sixty Govern a partir da galeria
Para configurar a integração do Infogix Data3Sixty Govern ao Microsoft Azure Active Directory, você precisará adicionar o Infogix Data3Sixty Govern da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Infogix Data3Sixty Govern da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Infogix Data3Sixty Govern**, selecione **Infogix Data3Sixty Govern** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infogix Data3Sixty Govern na lista de resultados](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o Infogix Data3Sixty Govern com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do Infogix Data3Sixty Govern é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Infogix Data3Sixty Govern.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Infogix Data3Sixty Govern, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Infogix Data3Sixty Govern](#create-an-infogix-data3sixty-govern-test-user)** – para ter um equivalente de Brenda Fernandes no Infogix Data3Sixty Govern vinculado à representação do usuário no Microsoft Azure Active Directory.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo Infogix Data3Sixty Govern.

**Para configurar o logon único do Microsoft Azure Active Directory com o Infogix Data3Sixty Govern, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Infogix Data3Sixty Govern**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

1. Na seção **Domínio e URLs do Infogix Data3Sixty Govern**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://data3sixty.com/ui`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.data3sixty.com/sso/acs`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do ao cliente do Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com) para obter esses valores.

1. O aplicativo Infogix Data3Sixty Govern espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar logon único attb](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
1. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | nome           | user.givenname |
    | sobrenome        | user.surname |
    | Nome de Usuário       | user.mail    |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/infogix-tutorial/tutorial_infogix_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/infogix-tutorial/tutorial_general_400.png)
    
1. Na seção **Infogix Data3Sixty Govern**, clique em **Configurar Infogix Data3Sixty Govern** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_configure.png) 

1. Para configurar o logon único no lado do **Infogix Data3Sixty Govern**, é necessário enviar **o Certificado (bruto) baixado, a URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** para a [equipe de suporte do Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/infogix-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/infogix-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/infogix-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/infogix-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Criar um usuário de teste do Infogix Data3Sixty Govern


O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Infogix Data3Sixty Govern. O Infogix Data3Sixty Govern dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Infogix Data3Sixty Govern, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Infogix Data3Sixty Govern.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Infogix Data3Sixty Govern, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Infogix Data3Sixty Govern**.

    ![O link do Infogix Data3Sixty Govern na lista de aplicativos](./media/infogix-tutorial/tutorial_infogix_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Infogix Data3Sixty Govern no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Infogix Data3Sixty Govern.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

