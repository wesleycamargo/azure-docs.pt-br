---
title: 'Tutorial: Integração do Microsoft Azure Active Directory com RedBrick Health | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o RedBrick Health.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 11452dfb94a5a1c8cf4734b1ef21a44cccafbea1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430501"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Tutorial: Integração do Microsoft Azure Active Directory com RedBrick Health

Neste tutorial, você aprenderá a integrar o RedBrick Health ao Microsoft Azure AD (Microsoft Azure Active Directory).

A integração do RedBrick Health com o Microsoft Azure AD fornece os seguintes benefícios:

- No Microsoft Azure AD, é possível controlar quem tem acesso ao RedBrick Health.
- Você pode permitir que os usuários façam logon automaticamente no RedBrick Health (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o RedBrick Health, os seguintes itens serão necessários:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do RedBrick Health

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o RedBrick Health da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-redbrick-health-from-the-gallery"></a>Adicionar o RedBrick Health da galeria
Para configurar a integração do RedBrick Health ao Microsoft Azure AD, será necessário adicionar o RedBrick Health da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RedBrick Health da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **RedBrick Health**, selecione **RedBrick Health** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![RedBrick Health na lista de resultados](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Microsoft Azure AD com o RedBrick Health com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do RedBrick Health é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no RedBrick Health.

No RedBrick Health, atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure AD com o RedBrick Health, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do RedBrick Health](#create-a-redbrick-health-test-user)** – para ter um equivalente de Brenda Fernandes no RedBrick Health que esteja vinculado à representação de usuário do Microsoft Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no Portal do Azure e configurará o logon único no aplicativo portal RedBrick Health.

**Para configurar o logon único do Microsoft Azure AD com o RedBrick Health, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **RedBrick Health**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

1. Na seção **Domínio e URLs do RedBrick Health**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do RedBrick Health](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `http://www.redbrickhealth.com`
    
    b. Na caixa de texto **URL de resposta**, insira uma URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Para o ambiente de produção: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Clique em **Mostrar configurações de URL avançadas**.
    
    ![Informações de logon único de Domínio e URLs do RedBrick Health](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > O valor do Estado de Retransmissão não é real. Atualize esse valor com o Estado de Retransmissão atual. Para obter esse valor, entre em contato com a [Equipe de suporte do RedBrick Health](https://home.redbrickhealth.com/contact/).

1. Seu aplicativo RedBrick Health espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. Essas declarações são específicas de cliente e dependem dos requisitos. As declarações opcionais a seguir são exemplos apenas, que você pode configurar para seu aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos.

    ![Configurar o logon único](./media/redbrickhealth-tutorial/attribute.png)

1. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:

    | Nome do atributo | Valor do atributo |
    | ---------------| ----------------|
    | nome principal | ********** |
    | ID do CLIENTE | ********** |
    | id do participante | ********** |
    
    > [!NOTE]
    > Estes valores são apenas para fins de referência. Você precisa definir os atributos de acordo com os requisitos da sua organização. Entre em contato com [Equipe de suporte do RedBrick Health](https://home.redbrickhealth.com/contact/) para obter mais informações sobre as declarações necessárias.
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
    
    ![Configurar o logon único](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o logon único](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/redbrickhealth-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do RedBrick Health**, clique em **Configurar o RedBrick Health** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** da **seção Referência Rápida.**

    ![Configuração do RedBrick Health](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

1. Para configurar o logon único no lado do **RedBrick Health**, é necessário enviar o **Certificado (Base64)** baixado e a **ID da Entidade SAML** para a [Equipe de suporte do RedBrick Health](https://home.redbrickhealth.com/contact/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/redbrickhealth-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/redbrickhealth-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/redbrickhealth-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-redbrick-health-test-user"></a>Criar um usuário de teste do RedBrick Health

Nesta seção, você cria um usuário chamado Brenda Fernandes no RedBrick Health. Trabalhe com [Equipe de suporte do RedBrick Health](https://home.redbrickhealth.com/contact/) para adicionar os usuários na plataforma do RedBrick Health. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Microsoft Azure concedendo acesso para RedBrick Health.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao RedBrick Health, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **RedBrick Health**.

    ![O link do RedBrick Health na lista de aplicativos](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do RedBrick Health no Painel de Acesso, você deverá entrar automaticamente no aplicativo RedBrick Health.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

