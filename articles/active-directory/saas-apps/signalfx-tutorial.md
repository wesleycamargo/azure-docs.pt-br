---
title: 'Tutorial: Integração do Azure Active Directory com SignalFx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 24a5c1a260cf86f0860e0292c2eb9527f976a363
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040503"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Tutorial: Integração do Azure Active Directory com SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx ao Azure Active Directory.

A integração do SignalFx ao Azure Active Directory oferece os benefícios a seguir:

- Você pode controlar no Azure Active Directory quem terá acesso ao SignalFx.
- Você pode permitir que seus usuários façam logon automaticamente no SignalFx (logon único) com suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o SignalFx, você precisará dos itens a seguir:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SignalFx

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar SignalFx da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-signalfx-from-the-gallery"></a>Adicionar SignalFx da galeria
Para configurar a integração do SignalFx ao Azure Active Directory, será necessário adicionar o SignalFx da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SignalFx a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SignalFx**, selecione **SignalFx** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SignalFx na lista de resultados](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o SignalFx, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do SignalFx é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no SignalFx.

Para configurar e testar o logon único do Azure Active Directory com o SignalFx, você precisará concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do SignalFx](#create-a-signalfx-test-user)** - para ter um equivalente de Brenda Fernandes no SignalFx que esteja vinculado à representação de usuário no Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo do SignalFx.

**Para configurar o logon único do Azure Active Directory com o SignalFx, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **SignalFx**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Na seção **Domínio e URLs do SignalFx**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://api.signalfx.com/v1/saml/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > O valor anterior não é um valor real. Você atualiza o valor com a URL de Resposta real, que é explicada no tutorial posteriormente.

4. O aplicativo SignalFx espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.   

    ![Configurar o logon único](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.
 
6. Na seção **Certificado de Autenticação SAML**, execute as seguintes etapas: 

    ![O link de download do Certificado](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    b. Clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/signalfx-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do SignalFx**, clique em **Configurar SignalFx** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** da **seção Referência Rápida.**

    ![Configuração do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Faça logon no site da empresa SignalFx como administrador.

10. Em SignalFx, na parte superior, clique em **Integrações** para abrir a página Integrações.

    ![Integração do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

11. Clique no bloco **Azure Active Directory** na seção **Serviços de Logon**.
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

12. Clique em **NOVA INTEGRAÇÃO** e na guia **INSTALAR** execute as seguintes etapas:
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Na caixa de texto **Nome**, digite um novo nome de integração, como **OurOrgName SAML SSO**.

    b. Copie o valor da **ID da Integração** e anexe com a **URL de Resposta** como `https://api.signalfx.com/v1/saml/acs/<integration ID>` na caixa de texto **URL de Resposta** da seção **Domínio e URLs do SignalFx**  no Portal do Azure.

    c. Clique em **Carregar Arquivo** para carregar o **Certificado codificado na Base64** baixado do Portal do Azure na caixa de texto **Certificado**.

    d. Na caixa de texto **URL do Emissor**, cole o valor da **ID da Entidade SAML** copiada do Portal do Azure.

    e. Na caixa de texto **URL de Metadados**, cole a **URL de Metadados de Federação de Aplicativos** que você copiou do Portal do Azure.

    f. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/signalfx-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/signalfx-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/signalfx-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/signalfx-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-signalfx-test-user"></a>Criar um usuário de teste do SignalFx

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SignalFx. O SignalFx dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o SignalFx, caso ele ainda não exista.

Quando um usuário iniciar o SignalFx do SSO de SAML pela primeira vez, a [Equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com) enviará um email contendo um link que o usuário deverá clicar para autenticar. Isso acontecerá somente na primeira vez que o usuário entrar. As tentativas subsequentes de logon não exigirão validação de email.

>[!Note]
>Se for necessário criar um usuário manualmente, contate a [Equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SignalFx.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SignalFx, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SignalFx**.

    ![O link do SignalFx na lista de Aplicativos](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SignalFx no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo SignalFx.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

